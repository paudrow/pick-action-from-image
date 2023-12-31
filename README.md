# Pick actions from an image

[<img alt="github" src="https://img.shields.io/badge/github-paudrow/pick action from image-8da0cb?style=for-the-badge&labelColor=555555&logo=github" height="20">](https://github.com/paudrow/pick-action-from-image)
[<img alt="crates.io" src="https://img.shields.io/crates/v/pick_action_from_image.svg?style=for-the-badge&color=fc8d62&logo=rust" height="20">](https://crates.io/crates/pick_action_from_image)
[<img alt="docs.rs" src="https://img.shields.io/badge/docs.rs-pick_action_from_image-66c2a5?style=for-the-badge&labelColor=555555&logo=docs.rs" height="20">](https://docs.rs/pick_action_from_image)
[<img alt="build status" src="https://img.shields.io/github/actions/workflow/status/paudrow/pick-action-from-image/ci.yaml?branch=main&style=for-the-badge" height="20">](https://github.com/paudrow/pick-action-from-image/actions/workflows/ci.yaml?query=branch%3Amain)

Pick the best action from a list of actions given an image and context.

This can be used for other AI models, but currently only has a model for [ChatGPT-4V](https://platform.openai.com/docs/guides/vision).

## Usage

Define an `ImagePath`:

```rust
// Picture of a cute dog
let url = Url::parse("https://www.petlandflorida.com/wp-content/uploads/2022/04/shutterstock_1290320698-1-scaled.jpg").unwrap();
let image_path = ImagePath::Url(&url);
```

Files can also be used:
```rust
let path = PathBuf::from("assets/my_image.jpeg");
let image_path = ImagePath::File(&path);
```

Then provide context for how actions should be picked. This can be a question or a statement.

```rust
let context = "What would make them happiest? They haven't eaten."; // Your question
```

Then define a list of actions to pick from. The description helps the AI understand what the action is and when it should be picked.

```rust
let actions = vec![
    Action {
        id: "give bone",
        description: "Give a delicious bone to chew on",
    },
    Action {
        id: "give belly rub",
        description: "Give a belly rub",
    },
    Action {
        id: "none",
        description: "Don't do anything",
    },
];
```

Then create an AI instance. Currently only ChatGPT-4V is implemented.

```rust
dotenv().ok();
let api_key = env::var("OPENAI_API_KEY").expect("API key not found");
let chat_gpt4v = ChatGpt4v { api_key: &api_key };
```

And finally, ask the AI about the image. You'll get back the best action as a `String`.

``` rust
//  Ask the AI about the image
let picked_action = pick_action_from_image(&chat_gpt4v, &context, &actions, &image_path)
    .await
    .unwrap();
println!("Picked action: {}", picked_action);
```

You can see a full example in the repository's `examples` folder.

## Setup

Make sure that you have [Rust installed](https://www.rust-lang.org/tools/install).

Then you should setup your `OPENAI_API_KEY` environment variable.
To do this, you can copy the `.env.example` file to `.env` and fill in the value.

You can find your API key in your [User Settings](https://help.openai.com/en/articles/4936850-where-do-i-find-my-api-key).
