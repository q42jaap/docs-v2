
## Why NextJS?

## Bootstrap a new app from the NextJS **learn-starter** template.

```sh
github % npx create-next-app nextjs-iot-starter --use-npm --example "https://github.com/vercel/next-learn/tree/master/basics/learn-starter"
```

`npx` installs required packages.

```sh
Need to install the following packages:
  create-next-app
Ok to proceed? (y) y
Creating a new Next.js app in /Users/me/github/nextjs-iot-starter.

Downloading files from repo https://github.com/vercel/next-learn/tree/master/basics/learn-starter. This might take a moment.

...

Success! Created nextjs-iot-starter at /Users/me/github/nextjs-iot-starter
Inside that directory, you can run several commands:

  npm run dev
    Starts the development server.

  npm run build
    Builds the app for production.

  npm start
    Runs the built app in production mode.

We suggest that you begin by typing:

  cd nextjs-iot-starter
  npm run dev

```

Start the development server for your nextjs-iot-starter app.

```sh

cd nextjs-iot-starter
```

Verify it runs.

```sh
npm run dev
```

Visit http://localhost:3000 in your browser.

## Set InfluxDB environment variables

Next.js provides an `env` module to configure environment variables.
Use an `.env.local` file to configure development secrets for your app
and prevent them being added to version control.
At startup, Next.js loads these variables into your Node environment to make them
available in `process.env`.

```sh
# .env.local

INFLUX_URL=http://192.168.1.2:8086
INFLUX_TOKEN=29Ye1KH9VkASPR2DSfRfFd82OwGD-5HWkBj0Ju_m-DTgT4PHakgweD3p87mp45Y633njDllKkD5wVc0zMCVhIw==
INFLUX_ORG=influx-jstirnaman

INFLUX_BUCKET=iot_center
INFLUX_BUCKET_AUTH=iot_center_devices
```
