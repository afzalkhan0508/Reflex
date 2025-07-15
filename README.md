# Reflex
Reflex is a library to build full-stack web apps in pure Python.  Key features:  Pure Python - Write your app's frontend and backend all in Python, no need to learn Javascript. Full Flexibility - Reflex is easy to get started with, but can also scale to complex apps. Deploy Instantly - After building, deploy your app with a single command. 
Introduction

Reflex is a library to build full-stack web apps in pure Python.

Key features:

- **Pure Python** - Write your app's frontend and backend all in Python, no need to learn Javascript.
- **Full Flexibility** - Reflex is easy to get started with, but can also scale to complex apps.
- **Deploy Instantly** - After building, deploy your app with a single command or host it on your own server.

## ⚙️ Installation

Open a terminal and run (Requires Python 3.10+):

```bash
pip install reflex
```

## 🥳 Create your first app

Installing `reflex` also installs the `reflex` command line tool.

Test that the install was successful by creating a new project. (Replace `my_app_name` with your project name):

```bash
mkdir my_app_name
cd my_app_name
reflex init
```

This command initializes a template app in your new directory.

You can run this app in development mode:

```bash
reflex run
```

You should see your app running at http://localhost:3000.

Now you can modify the source code in `my_app_name/my_app_name.py`. Reflex has fast refreshes so you can see your changes instantly when you save your code.

## 🫧 Example App

Let's go over an example: creating an image generation UI around [DALL·E](https://platform.openai.com/docs/guides/images/image-generation?context=node). For simplicity, we just call the [OpenAI API](https://platform.openai.com/docs/api-reference/authentication), but you could replace this with an ML model run locally.

&nbsp;

<div align="center">
<img src="https://raw.githubusercontent.com/reflex-dev/reflex/main/docs/images/dalle.gif" alt="A frontend wrapper for DALL·E, shown in the process of generating an image." width="550" />
</div>

&nbsp;

Here is the complete code to create this. This is all done in one Python file!

```python
import reflex as rx
import openai

openai_client = openai.OpenAI()


class State(rx.State):
    """The app state."""

    prompt = ""
    image_url = ""
    processing = False
    complete = False

    def get_image(self):
        """Get the image from the prompt."""
        if self.prompt == "":
            return rx.window_alert("Prompt Empty")

        self.processing, self.complete = True, False
        yield
        response = openai_client.images.generate(
            prompt=self.prompt, n=1, size="1024x1024"
        )
        self.image_url = response.data[0].url
        self.processing, self.complete = False, True


def index():
    return rx.center(
        rx.vstack(
            rx.heading("DALL-E", font_size="1.5em"),
            rx.input(
                placeholder="Enter a prompt..",
                on_blur=State.set_prompt,
                width="25em",
            ),
            rx.button(
                "Generate Image",
                on_click=State.get_image,
                width="25em",
                loading=State.processing
            ),
            rx.cond(
                State.complete,
                rx.image(src=State.image_url, width="20em"),
            ),
            align="center",
        ),
        width="100%",
        height="100vh",
    )

# Add state and page to the app.
app = rx.App()
app.add_page(index, title="Reflex:DALL-E")
```

## Let's break this down.

<div align="center">
<img src="https://raw.githubusercontent.com/reflex-dev/reflex/main/docs/images/dalle_colored_code_example.png" alt="Explaining the differences between backend and frontend parts of the DALL-E app." width="900" />
</div>

### **Reflex UI**

Let's start with the UI.

```python
def index():
    return rx.center(
        ...
    )
```

This `index` function defines the frontend of the app.

We use different components such as `center`, `vstack`, `input`, and `button` to build the frontend. Components can be nested within each other
to create complex layouts. And you can use keyword args to style them with the full power of CSS.
#*State*
Reflex represents your UI as a function of your state.

```python
class State(rx.State):
    """The app state."""
    prompt = ""
    image_url = ""
    processing = False
    complete = False

```

The state defines all the variables (called vars) in an app that can change and the functions that change them.

Here the state is comprised of a `prompt` and `image_url`. There are also the booleans `processing` and `complete` to indicate when to disable the button (during image generation) and when to show the resulting image.

### **Event Handlers**

```python
def get_image(self):
    """Get the image from the prompt."""
    if self.prompt == "":
        return rx.window_alert("Prompt Empty")

    self.processing, self.complete = True, False
    yield
    response = openai_client.images.generate(
        prompt=self.prompt, n=1, size="1024x1024"
    )
    self.image_url = response.data[0].url
    self.processing, self.complete = False, True
```

Within the state, we define functions called event handlers that change the state vars. Event handlers are the way that we can modify the state in Reflex. They can be called in response to user actions, such as clicking a button or typing in a text box. These actions are called events.

Our DALL·E. app has an event handler, `get_image` to which get this image from the OpenAI API. Using `yield` in the middle of an event handler will cause the UI to update. Otherwise the UI will update at the end of the event handler.

### **Routing**

Finally, we define our app.

```python
app = rx.App()
```

We add a page from the root of the app to the index component. We also add a title that will show up in the page preview/browser tab.

```python
app.add_page(index, title="DALL-E")
```

You can create a multi-page app by adding more pages.

## 📑 Resources

<div align="center">

</div>

## ✅ Status


🚀 Introducing [Reflex Build](https://build.reflex.dev/) — Our AI-Powered Builder
Reflex Build uses AI to generate complete full-stack Python applications. It helps you quickly create, customize, and refine your Reflex apps — from frontend components to backend logic — so you can focus on your ideas instead of boilerplate code. Whether you’re prototyping or scaling, Reflex Build accelerates development by intelligently scaffolding and optimizing your app’s entire stack.

Alongside this, [Reflex Cloud](https://cloud.reflex.dev) launched in 2025 to offer the best hosting experience for your Reflex apps. We’re continuously improving the platform with new features and capabilities.

Reflex has new releases and features coming every week! Make sure to :star: star and :eyes: watch this repository to stay up to date.
## License

Reflex is open-source and licensed under the [Apache License 2.0](https://raw.githubusercontent.com/reflex-dev/reflex/main/LICENSE).

Thanks!
Afzal khan

