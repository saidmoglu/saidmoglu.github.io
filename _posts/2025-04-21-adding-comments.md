---
layout: post
title: How I Added Comments to My Blog
subtitle: A step-by-step guide to implementing a static commenting system for Github Pages
tags: [blog, staticman, comments]
thumbnail-img: /assets/img/staticman/staticman.png
---

Scroll down to the bottom of this post and you will see a comment form. Would you believe me if I told you that it took me 4 hours of vibe coding (AI coding) to develop and deploy this commenting system?

I hope you wouldn't. Because that is not what happened.

Developing such a service would be a huge undertaking for a solo engineer, and current AI tools cannot really be of much help. In fact, I tried to get AI to help me deploy an existing commenting system called Staticman but it failed even doing that. The reasons for failure are:
1. AI keeps referring to outdated information. Staticman documentation is out of date.
2. AI guidelines about how to deploy the service to heroku, render etc. is equally based on outdated information.
3. Hallucinations everywhere.
4. Once you run into a problem at any point and ask AI to help, it starts going off-rails and recommends irrelevant, unhelpful, or over complicated solutions.

After all, I ended up doing what we always did: Carefully read the docs, and occasionally the code, understand how the pieces should fit together, and along with some googling I got it working. I will present the steps here.

### Why Staticman?

- **Data Ownership**: Comments are stored as YAML files in my own repository.
- **No External Dependencies**: Comments are rendered statically with the rest of the site.
- **Free and Open Source**: Well, staticman itself is open source but you do need to deploy it somewhere, which might not be free. For now I present a free solution below.
- **Privacy-Focused**: No tracking or ads.

![Staticman](/assets/img/staticman/staticman.png){: width="300px" .mx-auto.d-block :}

### Implementation Steps

{: .box-warning}
This is for my own blog, which is hosted on Github Pages. Also, the theme I am using, [Beautiful Jekyll](https://beautifuljekyll.com/), already supports Staticman integration, so I didn't have to deal with adding the commenting code.

1. First I created a fork of [the staticman repo](https://github.com/eduardoboucas/staticman), so that I could use my own commits on top of the original repo. Note that the repo has not been maintained for a while.
   - I changed the default port to 10000 in `config.js`

2. Go to [render](https://render.com), create an account, and create a new web service. 
   - Connect to your GitHub account and select the forked staticman repo as the source.
   - Choose any name. 
   - Language (or Runtime) is Node, build command is `npm install`, and start command is `npm start`.
   - Instance type is free. For a little blog site, this should be enough. Only downside is "Free instances spin down after periods of inactivity." which we will get to in a minute.
   - We will enter some environment variables later.
   - Deploy the service. You will be given an endpoint such as `https://my-staticman-instance.onrender.com`. Note that down.

3. Create a new app for staticman in your [github](https://github.com/settings/apps).
   - Choose a name.
   - Set the homepage URL to anything you like.
   - Set the webhook URL to `https://my-staticman-instance.onrender.com/v1/webhook`. Replace the endpoint part of the URL with your own.
   - Under permissions, expand repository permissions and set the following: 
      - Contents: Read & Write
      - Pull requests: Read & Write
   - Under subscribe to events section, select `Pull request'
   - "Where can this GitHub App be installed?" can be set to "Only on this account".
   - Click "create the app".
   - In App's settings, under General, create a private key. This will download a `.pem` file.
   - Also in App's settings, go to Install App section and install it to your github account. You can select to limit it to just your Github Pages repo.

4. Now go back to render and add the following environment variables to your service:
   - `GITHUB_APP_ID`: Copy from the App you created in step 3.
   - `GITHUB_PRIVATE_KEY`: Copy the contents of the `.pem` file you downloaded in step 3. Note that you copy everything so it looks like `-----BEGIN RSA PRIVATE KEY-----\nABCDFDFD...-----END RSA PRIVATE KEY-----`
   - `NODE_ENV`: production. Not sure if this has any effect.
   - `PORT`: 10000. This is the port you set in step 1. I think 10000 is default in render anyways.
   - `RSA_PRIVATE_KEY`: We need to create a private key as well. In terminal, run `ssh-keygen -m PEM -t rsa -b 4096 -C "staticman key" -f staticman_key` and copy the contents of the `staticman_key` file. Again it should look like `-----BEGIN RSA PRIVATE KEY-----\nABCDFDFD...-----END RSA PRIVATE KEY-----`

   Now you should be able to deploy the service. If all goes well, go to your deployment URL and you should see a simple message `Hello from Staticman version 3.0.0!`

5. Now we need to configure the service on our Github Pages side. Since I am using Beautiful Jekyll, I entered a few parameters in my `_config.yml`:

   ```yaml
   staticman:
   repository: your github pages repo
   branch: master
   endpoint: <deployment url>/v3/entry/github/
   ```

6. Test the commenting system, it should be working now.

### How It Works

When a visitor submits a comment:
1. The form data is sent to the Staticman instance.
2. Staticman creates a new file in the `_data/comments` directory of the repo.
3. The comment appears on the site after auto deploy by github is completed, which takes about 10-15 seconds.

### Adding reCAPTCHA
I additionally set up reCAPTCHA for spam protection. This is optional but highly recommended to prevent spam.

- Set it up on [Google](https://developers.google.com/recaptcha).
   - Select reCAPTCHA v2.
- Generate site and secret keys.
- Add them to the configuration in `_config.yml` and `staticman.yml`. You need to encrypt the secret key by going to `<deployment url>/v3/encrypt/<your-secret-key>` and copying the encrypted value.

### Handling Free Instance Spin Down

Render will spin down the staticman instance after a period of inactivity, 15 minutes to be exact. This means that the first comment submission after this period of inactivity will take longer to process (in my expeience, about 40 seconds). You can test this spin down effect by waiting for 15 minutes and then just going to your deployement URL. You will see a default page by Render that says "Application is Loading". After it loads, you will see the "Hello from Staticman" message.

Fortunetely, it isn't very hard to get around this limitation (I hope Render doesn't read my blog). To handle this, I added a simple self-ping mechanism to my Staticman repo that pings the instance every 10 minutes. This keeps the instance alive and ensures that the first comment submission is not delayed. 

In `server.js`:
```javascript
const logger = require('./lib/Logger')
const https = require('https')

...

  // In the StaticmanAPI class, add the following function:
  setupPinger() {
    const PING_INTERVAL = 10 * 60 * 1000; // 10 minutes in milliseconds
    const BACKEND_URL = 'YOUR DEPLOYMENT URL';

    setInterval(() => {
      https.get(BACKEND_URL, (resp) => {
        logger.info(`Self-ping performed. Status: ${resp.statusCode}`);
      }).on('error', (err) => {
        logger.info(`Self-ping failed: ${err.message}`);
      });
    }, PING_INTERVAL);

    logger.info('Self-ping mechanism initialized');
  }

  // In the start function, call setupPinger function
  start(callback) {
    this.instance = this.server.listen(config.get('port'), () => {
      this.setupPinger(); // Initialize the self-ping mechanism
      if (typeof callback === 'function') {
        callback(config.get('port'))
      }
    })
  }
```

Render has a limit on instance hours per month, but it is set to 750 hours per workspace, so if you don't have any other services running, you should be fine (24*31 = 744 hours max per month). Also, it doesn't seem like you are limited to a single workspace, so you could have multiple free services.

### Add Request Logging

In addition, Render requires paid subscription for viewing request logs, so I added some request logging to my Staticman instance to get around that.

In `server.js` inside `StaticmanAPI` class, add:
```javascript
  this.server = express()

  // Add the following
  this.server.use((req, res, next) => {
      const logData = {
        method: req.method,
        path: req.path,
        params: req.params,
        query: req.query,
        ip: req.ip || req.headers['x-forwarded-for'] || req.connection.remoteAddress,
        userAgent: req.headers['user-agent']
      }

      logger.info(`Incoming request: ${JSON.stringify(logData)}`)
      next()
    })
```

Then you can view request logs in Render dashboard page under logs.

### Results

Feel free to test it out by leaving a comment below!