# quali-chat-web

The [quali.chat](https://app.quali.chat) web client provides a web interface for token-gated community chats, supporting verified token holders across multiple blockchains.

## Getting Started

The easiest way to get started with quali.chat is to just use the hosted copy at <https://app.quali.chat>.

## Important Security Notes

### Separate domains

We do not recommend running quali.chat from the same domain name as your Matrix
homeserver. The reason is the risk of XSS (cross-site-scripting)
vulnerabilities that could occur if someone caused quali.chat to load and render
malicious user generated content from a Matrix API which then had trusted
access to quali.chat (or other apps) due to sharing the same domain.

We have put some coarse mitigations into place to try to protect against this
situation, but it's still not good practice to do it in the first place. See
<https://github.com/element-hq/element-web/issues/1977> for more details.

### Configuration best practices

Unless you have special requirements, you will want to add the following to
your web server configuration when hosting quali.chat web:

- The `X-Frame-Options: SAMEORIGIN` header, to prevent quali.chat web from being
  framed and protect from [clickjacking][owasp-clickjacking].
- The `frame-ancestors 'self'` directive to your `Content-Security-Policy`
  header, as the modern replacement for `X-Frame-Options` (though both should be
  included since not all browsers support it yet, see
  [this][owasp-clickjacking-csp]).
- The `X-Content-Type-Options: nosniff` header, to [disable MIME
  sniffing][mime-sniffing].
- The `X-XSS-Protection: 1; mode=block;` header, for basic XSS protection in
  legacy browsers.

[mime-sniffing]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types#mime_sniffing
[owasp-clickjacking-csp]: https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html#content-security-policy-frame-ancestors-examples
[owasp-clickjacking]: https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html

If you are using nginx, this would look something like the following:

```
add_header X-Frame-Options SAMEORIGIN;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
add_header Content-Security-Policy "frame-ancestors 'self'";
```

For Apache, the configuration looks like:

```
Header set X-Frame-Options SAMEORIGIN
Header set X-Content-Type-Options nosniff
Header set X-XSS-Protection "1; mode=block"
Header set Content-Security-Policy "frame-ancestors 'self'"
```

Note: In case you are already setting a `Content-Security-Policy` header
elsewhere, you should modify it to include the `frame-ancestors` directive
instead of adding that last line.

## Building From Source

quali.chat is a modular webapp built with modern ES6 and uses a Node.js build system.
Ensure you have the latest LTS version of Node.js installed.

Using `yarn` instead of `npm` is recommended. Please see the Yarn [install
guide](https://classic.yarnpkg.com/en/docs/install) if you do not have it already.

1. Install or update `node.js` so that your `node` is at least the current recommended LTS.
1. Install `yarn` if not present already.
1. Clone the repo: `git clone https://github.com/quali-chat/quali-chat-web.git`.
1. Switch to the quali-chat-web directory: `cd quali-chat-web`.
1. Install the prerequisites: `yarn install`.
    - If you're using the `develop` branch, then it is recommended to set up a
      proper development environment (see [Setting up a dev
      environment](./developer_guide.md#setting-up-a-dev-environment) below)
1. Configure the app by copying `config.sample.json` to `config.json` and
   modifying it. See the [configuration docs](docs/config.md) for details.
1. `yarn dist` to build a tarball to deploy. Untaring this file will give
   a version-specific directory containing all the files that need to go on your
   web server.

Note that `yarn dist` is not supported on Windows, so Windows users can run `yarn build`,
which will build all the necessary files into the `webapp` directory. The version of quali.chat web
will not appear in Settings without using the dist script. You can then mount the
`webapp` directory on your web server to actually serve up the app, which is
entirely static content.

## config.json

quali.chat supports a variety of settings to configure default servers, behaviour, themes, etc.
See the [configuration docs](docs/config.md) for more details.

## Labs Features

Some features of quali.chat may be enabled by flags in the `Labs` section of the settings.
Some of these features are described in [labs.md](https://github.com/quali-chat/quali-chat-web/blob/develop/docs/labs.md).

## Caching requirements

quali.chat requires the following URLs not to be cached, when/if you are serving quali.chat from your own webserver:

```
/config.*.json
/i18n
/home
/sites
/index.html
```

We also recommend that you force browsers to re-validate any cached copy of quali.chat on page load by configuring your
webserver to return `Cache-Control: no-cache` for `/`. This ensures the browser will fetch a new version of quali.chat on
the next page load after it's been deployed. Note that this is already configured for you in the nginx config of our
Dockerfile.

## Development

Please read through the following:

1. [Developer guide](./developer_guide.md)
2. [Code style](./code_style.md)
3. [Contribution guide](./CONTRIBUTING.md)

## Translations

To add a new translation, head to the [translating doc](docs/translating.md).

For a developer guide, see the [translating dev doc](docs/translating-dev.md).

## Copyright & License

Copyright (c) 2014-2017 OpenMarket Ltd. <br>
Copyright (c) 2017 Vector Creations Ltd. <br>
Copyright (c) 2017-2025 New Vector Ltd. <br>
Copyright (c) 2025 Keypair Establishment.

This fork is a customized version of [Element Web](https://github.com/element-hq/element-web), rebranded and modified as quali.chat web by Keypair Establishment and licensed under the GNU Affero General Public License (as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version).

Unless required by applicable law or agreed to in writing, software distributed under the Licenses is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the Licenses for the specific language governing permissions and limitations under the Licenses.
