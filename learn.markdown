---
layout: default
---

# Made By Developers,<br> For Developers

One of the main goals of Nova Forms is to make forms development as straightforward as possible.
This is achieved by reducing the concept of forms to a minimum.
We have identified three main components that are used in every form:
The *form component*, the *form data* and the *server action*.
Let's look at these components step by step.

## The Form Data

The form data is the struct that contains all the data that is collected by your form.
It is used for data validation and to transportation.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, Default)]
struct MyForm {
    first_name: String,
    last_name: String,
}
```

## The Server Action

The server action determines what happens with your form data after it is submitted.
It takes your form data that you have just defined, plus a metadata object that contains the
clients locale, time zone and other useful information.

```rust
#[server]
async fn on_submit(
    form_data: MyForm,
    meta_data: MetaData
) -> Result<(), ServerFnError> {
    println!("form data received: {:#?}", form_data);
    Ok(())
}
```

## The Form Component

The form component determines how the form will be rendered for both the web and PDF.
We use the `view!` macro provided by Leptos to build a form.
This makes it especially easy for Rust newbies to learn Nova Forms as the syntax is very similar to HTML.

```rust
#[component]
fn MyForm() {
    let submit_action = create_server_action::<OnSubmit>();

    view! {
        <NovaForm on_submit=submit_action>
            <Input<String> label="First Name" bind="first_name" />
            <Input<String> label="Last Name" bind="last_name" />
        </NovaForm>
    }
}
```

The `<NovaForm>` component contains all the required form inputs.
To create inputs, we use the `<Input>` component.
Based on the type given to this component, a corresponding HTML input field is created.
Input validation is automatically added to make sure that the input satisfies the constrains given by the required type.
Of course, you can also use standard HTML elements within the `<NovaForm>` component to add paragraphs, headings and more.

Finally, we associate the server action with the form to send the form data on submission.
That's it, you have just created your first Nova Form.
What we've seen so far is very similar to Leptos' `<ActionForm>`.
Of course, this only scratches the surface of what's possible with Nova Forms.

## Type-Based Validation

We have already seen that the `<Input>` component takes a type.
This type is used for validation as well as for determining the input type and additional input attributes.
Nova Forms provides lots of types that can be used right away. If none of these types fit your needs, you can easily implement your own type by using the `Datatype` Trait.

* `String`
* `NonEmptyString`
* `u8` up to `u128`
* `i8` up to `i128`
* `BigInt`
* `BigRational`
* `bool`
* `Accept`
* `Date`
* `Time`
* `DateTime`
* `Telephone`
* `Email`
* `Option<T>`

The only thing that is left to do is to provide custom validation error messages.

```rust
#[component]
fn MyForm() {
    provide_translation(move |err| match err {
        EmailError::InvalidFormat =>
            "Whoops, seems like you entered an invalid email!"
    });

    view! {
        <NovaForm>
            <Input<Email> label="Email" bind="email" />
        </NovaForm>
    }
}
```

## Nova Forms Components

Nova Forms comes with lots of useful components to easily build your form, and integration is incredibly easy!
In the following section we go over the most useful ones, but keep in mind that there are many more.
For a full overview of all components, please refer to the documentation.

### File Uploads

The `<FileUpload>` component handles file uploads for you.
Simply add the component and files will be automatically added to a database.
A `FileId` is passed with the form data so you can easily retrieve the file binaries from the database again.


```rust
#[derive(Serialize, Deserialize, Clone, Debug, Default)]
struct MyForm {
    files: Vec<FileId>
}

#[component]
fn MyForm() {
    view! {
        <NovaForm>
            <FileUpload label="File Upload" bind="files" />
        </NovaForm>
    }
}
```

### Repeatables

Easily add repeatable elements with the `<Repeatable>` component.
You can bind the component to a `Vec` in your form data that contains the input of the repeated fields.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, Default)]
struct MyForm {
    repeatable: Vec<Element>
}

#[derive(Serialize, Deserialize, Clone, Debug, Default)]
struct Element {
    first_name: String,
    last_name: String,
}

#[component]
fn MyForm() {
    view! {
        <NovaForm>
            <Repeatable bind="repeatable" itemitem = move |idx| view! {
                <Input<NonEmptyString> label="First Name" bind="first_name" />
                <Input<NonEmptyString> label="Last Name" bind="last_name" />
            } />
        </NovaForm>
    }
}
```

### Pagination

If your form gets large, you can add pagination with the `<Pages>` component.
Simply wrap parts of your form into a `<Page>` to add a new page to your form.
You can add control elements by adding a `<PageStepper>`.

```rust

#[component]
fn MyForm() {
    view! {
        <NovaForm>
            <Pages>
                <Page id="first-page" label="First Page">
                    <h1>"First Page"</h1>
                </Page>
                <Page id="second-page" label="Second Page">
                    <h1>"Second Page"</h1>
                </Page>
                <Page id="third-page" label="Third Page">
                    <h1>"Third Page"</h1>
                </Page>
            </Pages>
            <PageStepper />
        </NovaForm>
    }
}
```

## PDF Rendering

Nova Forms provides an easy way to render your form to a PDF. Simply pass your form component alongside with the form data to the `PdfGen` module, and out comes a beautiful, accessible PDF. This makes it easy to integrate Nova Forms with existing business flows.

```rust
#[server]
async fn on_submit(form_data: DemoForm, meta_data: MetaData) -> Result<(), ServerFnError> {
    use crate::app::NovaFormsContextProvider;

    let output_path = expect_context::<PdfGen>()
        .render_form(move || view! {
            <RenderContextProvider form_data=form_data meta_data=meta_data>
                <DemoForm />
            </RenderContextProvider>
        })
        .await?;

    println!("form successfully rendered: {:?}", output_path);

    Ok(())
}
```

## Translations

Nova Forms comes with translations by default.
use the `t!` macro instead of a plain string to add translations.
The translations are checked at compile time, meaning you never forget a translation again!

Use the powerful [i18n Ally](https://github.com/lokalise/i18n-ally) tool to edit translations within VS Code.
It even allows you to automatically generate missing translations by integrating the [DeepL API](https://www.deepl.com/).


```rust
#[component]
fn MyForm() {
    let i18n = use_i18n();

    view! {
        <NovaForm on_submit=submit_action>
            <Input<String> label=t!(i18n, first_name) bind="first_name" />
            <Input<String> label=t!(i18n, first_name) bind="last_name" />
        </NovaForm>
    }
}
```


## Learn More

Please refer to the documentation or the demo project to learn more about Nova Forms.

<div class="button-row">
    <a class="button" href="https://docs.rs/nova-forms/latest/nova_forms/">Documentation</a>
    <a class="button" href="https://github.com/nova-forms/nova-forms-demo">Demo Project</a>
</div>