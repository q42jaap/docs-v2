

## Create your app

[Next.js](https://nextjs.org/) provides a framework and tools to help you quickly build a full-stack JavaScript application with production-level features and minimal setup.

To begin, use `npx` to create the `nextjs-iot-starter` app from the from the NextJS [learn-starter template](https://github.com/vercel/next-learn/tree/master/basics/learn-starter).

```sh
npx create-next-app nextjs-iot-starter --use-npm --example "https://github.com/vercel/next-learn/tree/master/basics/learn-starter"
```

After the installation completes, go into your `nextjs-iot-starter` directory and start the development server.

```sh
cd nextjs-iot-starter
npm run dev
```

To verify you can access the app, visit [http://localhost:3000](http://localhost:3000) in your browser.

## Install InfluxDB client library

If you haven't already, [create your app](#create-your-app).

From your app directory, install `@influxdata/influxdb-client` for the following write and query API features:

- Query data with the Flux language.
- Write data to InfluxDB.
- Batch data in the background.
- Retry requests automatically on failure.

```sh
npm i @influxdata/influxdb-client
```

## Install InfluxDB client library for management APIs

From your app directory, install `@influxdata/influxdb-client-apis` to create, modify, and delete buckets, tasks, authorizations, and other InfluxDB resources.

```sh
npm i @influxdata/influxdb-client-apis
```

## Set InfluxDB environment variables

InfluxDB client libraries require configuration properties from your InfluxDB environment.
Typically, you'll provide the following properties as environment variables accessible by your application:

- `INFLUX_URL`
- `INFLUX_TOKEN`
- `INFLUX_ORG`
- `INFLUX_BUCKET`
- `INFLUX_BUCKET_AUTH`

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

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

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

## Create a shared layout in a custom app

Create a shared UI layout that applies a header, footer, and CSS to all `pages` in your app.
To create the shared layout, do the following:

1. [create a shared layout component](): Add a `Layout` component that renders the header, footer, and CSS.
2. [create a custom app component](): Add a custom app that imports your `Layout` component to wrap `/pages` components.

### Create a shared layout component

To add the `Layout` component, create a `pages/_app.js` file that contains the following:

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

```js
import Head from 'next/head'

export default function Layout({ children }) {
  return (
    <div className="container">
      <Head>
        <title>IoT Starter</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main>{children}</main>

      <footer>
        <a
          href="https://vercel.com?utm_source=create-next-app&utm_medium=default-template&utm_campaign=create-next-app"
          target="_blank"
          rel="noopener noreferrer"
        >
          Powered by{' '}
          <img src="/vercel.svg" alt="Vercel" className="logo" />
        </a>
      </footer>

      <style jsx global>{`
        .container {
          min-height: 100vh;
          padding: 0 0.5rem;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
        }

        main {
          padding: 5rem 0;
          flex: 1;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
        }

        footer {
          width: 100%;
          height: 100px;
          border-top: 1px solid #eaeaea;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        footer img {
          margin-left: 0.5rem;
        }

        footer a {
          display: flex;
          justify-content: center;
          align-items: center;
        }

        a {
          color: inherit;
          text-decoration: none;
        }

        form input {
          margin-left: 0.5rem;
        }

        .title a {
          color: #0070f3;
          text-decoration: none;
        }

        .title a:hover,
        .title a:focus,
        .title a:active {
          text-decoration: underline;
        }

        .title {
          margin: 0;
          line-height: 1.15;
          font-size: 4rem;
        }

        .title,
        .description {
          text-align: center;
        }

        .description {
          line-height: 1.5;
          font-size: 1.5rem;
        }

        code {
          background: #fafafa;
          border-radius: 5px;
          padding: 0.75rem;
          font-size: 1.1rem;
          font-family: Menlo, Monaco, Lucida Console, Liberation Mono,
            DejaVu Sans Mono, Bitstream Vera Sans Mono, Courier New, monospace;
        }

        .grid {
          display: flex;
          align-items: center;
          justify-content: center;
          flex-wrap: wrap;

          max-width: 800px;
          margin-top: 3rem;
        }

        .card {
          margin: 1rem;
          flex-basis: 45%;
          padding: 1.5rem;
          text-align: left;
          color: inherit;
          text-decoration: none;
          border: 1px solid #eaeaea;
          border-radius: 10px;
          transition: color 0.15s ease, border-color 0.15s ease;
        }

        .card:hover,
        .card:focus,
        .card:active {
          color: #0070f3;
          border-color: #0070f3;
        }

        .card h3 {
          margin: 0 0 1rem 0;
          font-size: 1.5rem;
        }

        .card p {
          margin: 0;
          font-size: 1.25rem;
          line-height: 1.5;
        }

        .card .alert.alert-danger {
            color: #721c24;
            background-color: #f8d7da;
            border-color: #f5c6cb;
        }

        .logo {
          height: 1em;
        }

        @media (max-width: 600px) {
          .grid {
            width: 100%;
            flex-direction: column;
          }
        }

        html,
        body {
          padding: 0;
          margin: 0;
          font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
            Oxygen, Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue,
            sans-serif;
        }

        * {
          box-sizing: border-box;
        }
      `}</style>
    </div>
  )
}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

### Create a custom app component

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

To add the custom app component, create a `pages/_app.js` file that contains the following:

```js
import Layout from './_layout'

export default function IotStarter({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  )
}
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

### Copy the list devices example

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

In the example below, the `getDevices(deviceId)` function queries for registered devices, processes the data, and returns a Promise with the result.
If you invoke the function as `getDevices()` (without a `deviceId`), it retrieves all `deviceauth` points and returns a Promise of `{ DEVICE_ID: ROW_DATA }`.
To add a `_devices` module that exports the `getDevices(deviceId)` function, do the following:

1. In your `/pages/api/devices` directory, create the file `_devices.js`.
2. Paste the following example:

```js
{{< get-shared-text "api/v2.0/client-library-examples/nodejs/server/devices/_devices.js" >}}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

### Create the API route

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

The example below exports a server request `handler` function that listens for requests to `/api/devices` and responds with the [`getDevices(deviceId)`](#copy-the-list-devices-example) result.

1. In your `/pages/api/devices` directory, create the file `index.js`.
2. Paste the following example:

```js
{{< get-shared-text "api/v2.0/client-library-examples/nodejs/server/devices/index.js" >}}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

Next, learn how to add an endpoint that [registers a device](#register-a-device).

## Register a device

In **IoT Starter**, a _registered device_ is a point that contains your device ID and authorization details (API token and authorization ID).
A device authorization defines _read_ and _write_ permissions on `INFLUX_BUCKET`.
To register devices, you'll add an `/api/devices/create` route that passes requests
to InfluxDB and returns the new authorization.
`/api/devices/create` will use the following `/api/v2` InfluxDB API endpoints:

- `POST /api/v2/query`: to query `INFLUX_BUCKET_AUTH` for a registered device
- `GET /api/v2/buckets`: to get the bucket ID for `INFLUX_BUCKET`
- `POST /api/v2/authorizations`: to create an authorization for the device
- `POST /api/v2/write`: to write the device authorization to `INFLUX_BUCKET_AUTH`

- [Create an authorization for the device](#create-an-authorization-for-the-device)
- [Write the device authorization to a bucket]()
- [Copy the register device example](#copy-the-register-device-example)

### Get the bucket ID

To create an authorization with _read_-_write_ permission to `INFLUX_BUCKET`, you must pass the bucket ID.
To get the bucket ID, use the InfluxDB client library to send a `GET` request to the `/api/v2/buckets` InfluxDB API endpoint.

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

The example below instantiates the InfluxDB client and BucketsAPI client, calls the BucketsAPI `getBuckets` function, and returns the bucket ID.

```js
import { InfluxDB } from '@influxdata/influxdb-client'
import { BucketsAPI } from '@influxdata/influxdb-client-apis'

const influxdb = new InfluxDB({url: process.env.INFLUX_URL, token: process.env.INFLUX_TOKEN})
const bucketsAPI = new BucketsAPI(influxdb)

async function getBucketId() { 
  const buckets = await bucketsAPI.getBuckets({name: process.env.INFLUX_BUCKET, orgID: process.env.INFLUX_ORG})
  return buckets.buckets[0]?.id
}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

### Create an authorization for the device

To create an authorization and receive an API token for the device, use the InfluxDB client library to send a `POST` request to the `/api/v2/authorizations` InfluxDB API endpoint.
In your request body, pass an authorization with the following:
- description `IoTCenterDevice: DEVICE_ID` 
- array of permissions for the [bucket ID](#get-the-bucket-id)

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

The example below does the following:

- instantiates the InfluxDB client, AuthorizationsAPI client, and BucketsAPI client
- calls the BucketsAPI `getBuckets` function and returns the bucket ID
- calls the AuthorizationsAPI `postAuthorization` function and returns the new authorization

```js
import { InfluxDB } from '@influxdata/influxdb-client'
import { AuthorizationsAPI, BucketsAPI } from '@influxdata/influxdb-client-apis'

const influxdb = new InfluxDB({url: process.env.INFLUX_URL, token: process.env.INFLUX_TOKEN})
async function postAuthorization(deviceId) {
  const authorizationsAPI = new AuthorizationsAPI(influxdb)
  const bucketsAPI = new BucketsAPI(influxdb)
  const DESC_PREFIX = 'IoTCenterDevice: '

  const buckets = await bucketsAPI.getBuckets({name: process.env.INFLUX_BUCKET, orgID: process.env.INFLUX_ORG})
  const bucketId = buckets.buckets[0]?.id

  const auth = await authorizationsAPI.postAuthorizations({
    body: {
      orgID: process.env.INFLUX_ORG,
      description: DESC_PREFIX + deviceId,
      permissions: [
        {
          action: 'read',
          resource: {type: 'buckets', id: bucketId, orgID: process.env.INFLUX_ORG},
        },
        {
          action: 'write',
          resource: {type: 'buckets', id: bucketId, orgID: process.env.INFLUX_ORG},
        },
      ],
    },
  })

  return auth
}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

Next, [write the device authorization to a bucket](#write-the-device-authorization-to-a-bucket).

### Write the device authorization to a bucket

After you create the device authorization, write a point with device and authorization details to `INFLUX_BUCKET_AUTH`.
Storing the device authorization in a bucket enables the following:

- report device authorization history
- manage devices with and without tokens
- assign the same token to multiple devices
- refresh tokens

To write a point to InfluxDB, use the InfluxDB client library to send a `POST` request to the `/api/v2/write` InfluxDB API endpoint.
In your request body, pass a point that contains the following elements:

| Element     | Name       | Value                     |
|:-------     |:-------- --|:--------------------------|
| measurement |            | `deviceauth`              |
| tag         | `deviceId` | device ID                 |
| field       | `key`      | authorization ID          |
| field       | `token`    | authorization (API) token |g

### Copy the register device example

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[Node.js](#nodejs)
{{% /code-tabs %}}
{{% code-tab-content %}}

The example below exports a server request `handler` function that does the following:

1. accepts a device ID in the request body
2. queries `INFLUX_BUCKET_AUTH` and responds with an error if an authorization exists for the device
3. retrieves the `INFLUX_BUCKET` bucket ID
4. creates a device authorization with _read_-_write_ permission to `INFLUX_BUCKET`
5. writes the device ID and authorization to `INFLUX_BUCKET_AUTH`
6. responds with `HTTP 200` when the write request completes

7. In your `/pages/api/devices` directory, create the file `create.js` (to use [Next.js dynamic routes](https://nextjs.org/docs/api-routes/dynamic-api-routes))
8. Paste the following example:

```js
{{< get-shared-text "api/v2.0/client-library-examples/nodejs/server/devices/create.js" >}}
```

{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}
