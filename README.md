# Tailwind CSS for Rails

[Tailwind CSS](https://tailwindcss.com) is a utility-first CSS framework packed with classes like flex, pt-4, text-center and rotate-90 that can be composed to build any design, directly in your markup.

This gem wraps [the standalone executable version](https://tailwindcss.com/blog/standalone-cli) of the Tailwind CSS 3 framework. These executables are platform specific, so there are actually separate underlying gems per platform, but the correct gem will automatically be picked for your platform. Supported platforms are Linux x64, macOS arm64, macOS x64, and Windows x64. (Note that due to this setup, you must install the actual gems – you can't pin your gem to the github repo.)

You can customize the Tailwind build through the `config/tailwind.config.js` file, just like you would if Tailwind was running in a traditional node installation. All the first-party plugins are supported.

The installer will create your Tailwind input file in `app/assets/stylesheets/application.tailwind.css`. This is where you import the plugins you want to use, and where you can setup your custom `@apply` rules. When you run `rails tailwindcss:build`, this input file will be used to generate the output in `app/assets/builds/tailwind.css`. That's the output CSS that you'll include in your app (the installer automatically configures this, alongside the Inter font as well).

If you need to use a custom input or output file, you can run `bundle exec tailwindcss` to access the platform-specific executable, and give it your own build options.

When you're developing your application, you want to run Tailwind in watch mode, so changes are automatically reflected in the generated CSS output. You can do this either by running `rails tailwindcss:watch` as a separate process, or by running `./bin/dev` which uses [foreman](https://github.com/ddollar/foreman) to starts both the Tailwind watch process and the rails server in development mode.


## Installation

With Rails 7 you can generate a new application preconfigured with Tailwind by using `--css tailwind`. If you're adding Tailwind later, you need to:

1. Run `./bin/bundle add tailwindcss-rails`
2. Run `./bin/rails tailwindcss:install`

## Using the included TailwindCSS generator templates

Included are several pre-styled templates. 

```
Tailwindcss:
  tailwindcss:controller
  tailwindcss:mailer
  tailwindcss:scaffold
 ```

Simply prepend `tailwindcss:` to the generator type for some styling.

```
rails generate tailwindcss:scaffold Post title:string content:text 
```
These templated styles have been set up with a container element in mind. This element is added to `views/layouts/application.html.erb` during the above install process.
```
<main class="container mx-auto mt-28 px-5 flex">
  <%= yield %>
</main>
```

## Building in production

The `tailwindcss:build` is automatically attached to `assets:precompile`, so before the asset pipeline digests the files, the Tailwind output will be generated.


## Conflict with sassc-rails

Tailwind uses modern CSS features that are not recognized by the `sassc-rails` extension that was included by default in the Gemfile for Rails 6. In order to avoid any errors like `SassC::SyntaxError`, you must remove that gem from your Gemfile.


## Troubleshooting

Some common problems experienced by users ...

### ERROR: Cannot find the tailwindcss executable for &lt;supported platform&gt;

Some users are reporting this error even when running on one of the supported native platforms:

- arm64-darwin
- x64-mingw32
- x86_64-darwin
- x86_64-linux
- aarch64-linux

#### Check Bundler PLATFORMS

A possible cause of this is that Bundler has not been told to include native gems for your current platform. Please check your `Gemfile.lock` file to see whether your native platform is included in the `PLATFORMS` section. If necessary, run:

``` sh
bundle lock --add-platform <platform-name>
```

and re-bundle.


#### Check BUNDLE_FORCE_RUBY_PLATFORM

Another common cause of this is that bundler is configured to always use the "ruby" platform via the
`BUNDLE_FORCE_RUBY_PLATFORM` config parameter being set to `true`. Please remove this configuration:

``` sh
bundle config unset force_ruby_platform
# or
bundle config set --local force_ruby_platform false
```

and re-bundle.

See https://bundler.io/man/bundle-config.1.html for more information.


### "No such file or directory" running on Alpine (musl)

When running `tailwindcss` on an Alpine system, some users report a "No such file or directory" error message.


#### Install gnu libc compatibility

The cause of this is the upstream `tailwindcss` binary executables being built on a gnu libc system, making them incompatible with standard musl libc systems.

A fix for this has been proposed upstream at https://github.com/tailwindlabs/tailwindcss/discussions/6785, but in the meantime a workaround is to install compatibility libraries:

``` sh
apk add build-base gcompat
```


## License

Tailwind for Rails is released under the [MIT License](https://opensource.org/licenses/MIT).
Tailwind CSS is released under the [MIT License](https://opensource.org/licenses/MIT).
The Inter font is released under the [SIL Open Font License, Version 1.1](https://github.com/rsms/inter/blob/master/LICENSE.txt).
