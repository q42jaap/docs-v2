
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

## Install InfluxDB client library

Install `@influxdata/influxdb-client` for the following write and query API features:

- Query data using the Flux language
- Write data to InfluxDB
- batch data in the background
- retry automatically on failure

```sh
npm i @influxdata/influxdb-client
```

## Install InfluxDB client library for management APIs

Install `@influxdata/influxdb-client-apis` to create, modify, and delete buckets, tasks, authorizations, and other InfluxDB resources.

```sh
npm i @influxdata/influxdb-client-apis
```

## Set InfluxDB environment variables

Next.js provides an `env` module to configure environment variables.
Create an `.env.local` file to store development configuration settings for your app
and prevent them being added to version control.

```sh
# .env.local

INFLUX_URL=http://192.168.1.2:8086
INFLUX_TOKEN=29Ye1KH9VkASPR2DSfRfFd82OwGD-5HWkBj0Ju_m-DTgT4PHakgweD3p87mp45Y633njDllKkD5wVc0zMCVhIw==
INFLUX_ORG=48c88459ee424a04

INFLUX_BUCKET=iot_center
INFLUX_BUCKET_AUTH=iot_center_devices
```

At startup, Next.js loads the settings in your Node.JS environment variables.
To use the settings, access them in `process.env`, as in the following example:

```js
import { InfluxDB } from '@influxdata/influxdb-client'
import { BucketsAPI } from '@influxdata/influxdb-client-apis'

const influxdb = new InfluxDB({url: process.env.INFLUX_URL, token: process.env.INFLUX_TOKEN})
const bucketsAPI = new BucketsAPI(influxdb)
bucketsAPI.getBuckets({name: process.env.INFLUX_BUCKET, orgID: process.env.INFLUX_ORG})
```

## Create a custom app with a shared layout

### Create a shared layout component

Create a `pages/_index.js` file that contains the following;

### Create a custom app component

Create a `pages/_app.js` file that contains the following:


## Register a device

To register a device, you'll use the following `/api/v2` InfluxDB API endpoints:

- `POST /api/v2/query`
- `GET /api/v2/buckets`
- `POST /api/v2/authorizations`
- `POST /api/v2/write`

- [Create an authorization for the device]()
- [Write the device authorization data to a bucket]()
- [Copy the example]()

### Copy the example

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

The sample below exports a server request `handler` function that creates device authorizations, writes them to a bucket, and returns the new data.
- 
In your `/pages/api/devices` directory, create the file `create.js` and paste the following example:

```js
{{< get-shared-text "api/v2.0/client-library-examples/nodejs/server/devices/create.js" >}}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}





## List devices

To retrieve registered devices, send a Flux query to the `POST /api/v2/query` InfluxDB API endpoint.

- [Create the Flux query]()
- [Execute the query and process results]()
- [Copy the sample code]()

### Create the Flux query

To retrieve registered devices from your `INFLUX_BUCKET_AUTH` bucket, get the last row of each [series]() that contains a `deviceauth` measurement. The example below returns rows that contain the `key` field (authorization ID) and excludes rows that contain a `token` field (to avoid exposing sensitive token values).

```js
// Flux query finds devices
 from(bucket:`${INFLUX_BUCKET_AUTH}`)
      |> range(start: 0)
      |> filter(fn: (r) => r._measurement == "deviceauth" and r._field != "token")
      |> last()
```

### Execute the query and process results

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

To send the query and process the results, use the QueryAPI `queryRows(query, consumer)` method.
`queryRows` executes the `query` and provides the Annotated CSV result as an Observable to the `consumer`.
`queryRows` has the following signature (in TypeScript):

```ts
  queryRows(
    query: string | ParameterizedQuery,
    consumer: FluxResultObserver<string[]>
  ): void
```

{{% caption %}}[@influxdata/influxdb-client-js QueryAPI, line 92](https://github.com/influxdata/influxdb-client-js/blob/3db2942432b993048d152e0d0e8ec8499eedfa60/packages/core/src/QueryApi.ts#L92){{% /caption %}}

The `consumer` that you provide must implement the [`FluxResultObserver` interface](https://github.com/influxdata/influxdb-client-js/blob/3db2942432b993048d152e0d0e8ec8499eedfa60/packages/core/src/results/FluxResultObserver.ts#L7) that provides the following callback functions:
- `next(row, tableMeta)`: processes the next row and table metadata (e.g., by transforming data for the response)
- `error(error)`: receives and handles errors (e.g., by rejecting the Promise)
- `complete()`: called and signals when all rows have been consumed (e.g., by resolving the Promise)

To learn more about Observers, see the [RxJS Guide](https://rxjs.dev/guide/observer)
{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

### Copy the sample code

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

The sample below exports a `getDevices(deviceId)` function.
If you invoke the function as `getDevices()` (without a `deviceId`), it retrieves all `deviceauth` points and returns a Promise of `{DEVICE_ID: ROW_DATA }`.
In your `/pages/api/devices` directory, create the file `_devices.js` and paste the following sample code:

```js
{{< get-shared-text "api/v2.0/client-library-examples/nodejs/server/devices/_devices.js" >}}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}