# RYSPEC

*In this article you will learn how RYSPEC works*

---

## What is RYSPEC? 

RYSPEC is part of our RYS software which contain testing RYSes. In our case you can test from main Rails appplication as well as directly from RYS. 

---

### Test via Rails aplication

> Tests are located in `RS_ROOT/spec`.

1. First you have to succesfully run tests you have to use the right frameworks. For test purposes, use these frameworks:
      - [rpsec](https://github.com/rspec/rspec-metagem)
      - [rspec-rails](https://github.com/rspec/rspec-rails).

2. As a second, use correct factories. We are using [factory_bot](https://github.com/thoughtbot/factory_bot)

> *factory_bot* is a fixtures replacement with a straightforward definition syntax, support for multiple build strategies (*saved instances, unsaved instances, attribute hashes, and stubbed objects*), and support for multiple factories for the same class (*user, admin_user, and so on*), including factory inheritance.

For your folder structure, use `spec/factorries` for your factories and `spec/support` for your support files. In support directory files are automatically loaded. 


### Test dirrectly from RYS

1. You can test directly from RYS but you will need some [dummy application](https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Hello_RYS/Getting-started-with-RYS.md). RYSPEC will look for them in `DUMMY_PATH` (which is environment variable) and `test/dummy` directory.

2. After that you can run them with
```
bundle install
```
And
```
rspec
```

>If you are interested in testing from main application just run `bundle exec rake RYS_NAME:spec`