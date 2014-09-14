# jthoober

A service to receive github webhook events & run scripts in response. Run custom testing or deploys in response to pushes. Built on top of rvagg's [github-webhook-handler](https://github.com/rvagg/github-webhook-handler) and mcavage's [restify](http://mcavage.me/node-restify/).

[![Tests](http://img.shields.io/travis/ceejbot/jthoober.svg?style=flat)](http://travis-ci.org/ceejbot/jthoober)  ![Coverage](http://img.shields.io/badge/coverage-88%25-green.svg?style=flat)   [![Dependencies](http://img.shields.io/david/ceejbot/jthoober.svg?style=flat)](https://david-dm.org/ceejbot/jthoober)

## Usage

Set up jthoober somewhere that github has access to. Create a shared secret for github to send to the webhook & make a note of it. Run jthoober like this:

```shell
Usage: jthoober --rules path/to/rules.js --secret sooper-sekrit

Options:
  --rules, -r  path to the rules file     [required]
  --secret     shared secret with github  [required]
  -p, --port   port to listen on          [default: 5757]
  -h, --host   host to bind to            [default: "localhost"]
  --mount      path to mount routes on    [default: "/webhook"]
  --help       Show help
```

I like to use nginx to terminate tls then proxy pass through to jthoober.

The rules file must export an array of hashes; each hash is passed to the Rule constructor to make an object. (NOTE: I will make this smarter than that before publishing this.) Set up rules that match repos to scripts to execute when jthoober receives an event. Here are some examples:

```javascript
module.exports =
[
    { pattern: /jthoober/, event: '*', script: '/usr/local/bin/fortune' },
    { pattern: /request/, event: 'push', script: './example-script.sh', passargs: true },
];
```

Rules with `passargs` set will receive the repo name as the first script argument.

Set up a webhook for a project on github. Point it to your jthoober location & give it the secret string you created earlier. Observe that the test payload makes it through.

## Endpoints

`/webhook` - route that responds to the webhook. Configurable; pass `--mount /foo` to the runner to mount the handler on `/foo` instead.

`/ping` - responds with status ok.

## Notes

`j'thoob` is the official pronunciation of `gi-thub`, aka the site this code is hosted on.

## TODO

Pass more stuff from the hook event to the bash script. repo branch hash? Why not allow rules to be arbitrary node code? Or just define a handler API? But bash is so handy.

Do something useful in response to the ping event, if only because it's handy for debugging.

## License

ISC