---
layout: default
---

# Build Your Online Form<br> In Minutes

Follow these steps to install the prerequesites and use our template to get started quickly.

## Prerequesites

Install the Rust toolchain using the command below. More information is available on the [Rust website](https://www.rust-lang.org/tools/install).

```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

After installing Rust, the `cargo` command line tool should be available. Next, install the [Leptos](https://github.com/leptos-rs/cargo-leptos) command line tool to easily interact with Leptos projects.

```sh
cargo install --locked cargo-leptos
```

Install the [paged.js]() comannd line tool. This is required for the PDF generation service.

```sh
npm install -g pagedjs-cli pagedjs
```

## Starter Template

*The following sections are work in progress and may not work just yet.*

We provide a starter template to quickly set up new projects. Run the following command to generate a new project.

```sh
cargo leptos new --git nova-forms/start
```

Change into your new project directory and compile it with automatic rebuilds and browser live-reload.

```sh
cargo leptos watch
```

## Happy Coding!

That was already it! You can continue from this starter template to build your own form. Refer to the documentation and the examples for additional help.

We highly recommend using [VS Code](https://code.visualstudio.com/) for development alongside with the recommended plugins. The plugins will be automatically recommended to you once you open the starter template.

<div class="button-row">
    <a class="button" href="https://docs.rs/nova-forms/latest/nova_forms/">Documentation</a>
    <a class="button" href="https://github.com/nova-forms/nova-forms-demo">Demo Project</a>
</div>