# redis-on-workers

Connect to your Redis server using `cloudflare:sockets`.

This package is designed to work with Cloudflare Workers, but it can also be used in node.js thanks to the implementation of [`cloudflare:sockets` for node.js](https://github.com/Ethan-Arrowood/socket).

## Installation

```sh
npm install redis-on-workers
```

## Usage

### Minimal

This is the minimal example to connect to a Redis server.

```ts
import { createRedis } from "redis-on-workers";

const redis = createRedis("redis://<username>:<password>@<host>:<port>");

await redis.send("SET", "foo", "bar");

const value = await redis.send("GET", "foo");

console.log(value); // bar

// remember to close the connection after use, or use `redis.sendOnce`.
await redis.close();
```

### Raw Uint8Array

This is useful if you want to store binary data. For example, you can store protobuf messages in Redis.

```ts
import { createRedis } from "redis-on-workers";

const redis = createRedis("redis://<username>:<password>@<host>:<port>");

await redis.sendRaw("SET", "foo", "bar");

const value = await redis.sendRawOnce("GET", "foo");

const decoder = new TextDecoder();

console.log(decoder.decode(value)); // bar
```

### Node.js

Please install the node.js polyfill for `cloudflare:sockets` to use this package in node.js.

```sh
npm install @arrowood.dev/socket
```

## API

### `createRedis(options: CreateRedisOptions | string): RedisInstance`

Create a new Redis client, does NOT connect to the server yet, the connection will be established when the first command is sent.

Or you can start connection immediately by using `redis.startConnection()`.

### `CreateRedisOptions`

- `url` (string): The URL of the Redis server.
- `tls` (boolean): Whether to use TLS. Default: `false`.
- `logger` (function): A function to log debug messages.
- `connectFn` (function): Polyfill for `cloudflare:sockets`'s `connect` function if you're using it in node.js. Default: `undefined`.

## Publishing (Vase)

1. Make and test your commits to the master branch.
2. Update CHANGELOG.md with a summary of changes.
3. Bump the version in package.json as its own commit.
4. Push the changes to the remote repository.
5. Create a new tag using `git tag v<new-version>`
6. Push the tag to the remote repository using `git push origin v<new-version>`.
7. Github actions workflow will automatically publish the package to npm because of the new tag.

> **Note**\
> This package is published to the GitHub Packages registry (not npmjs.com).
> The publishing step in GitHub Actions uses the built-in `${{ secrets.GITHUB_TOKEN }}` for authentication, which does **not** require any manually configured secret.
