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
