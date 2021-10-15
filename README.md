## Steps to install & Run:

```
bundle install
bundle exec jekll serve
```

## Troubleshooting

- If one gets the error `Load error: cannot load such file – webrick` while doing `bundle install` on mac, one should add `gem "webrick"` and then `bundle install` again.
  - This happened to me because I had done `bundle upgrade`.