# Setup Environment

- verify gcc is installed in codespace
```
gcc --version
```

- verify git is installed in codespace

```
git --version
```

- install & verify rust is installed in codespace
```
curl https://sh.rustup.rs --proto 'https' --tlsv1.2 --fail --silent --show-error | sh

. $HOME ...

rustc --version
```

- install & verify github copilot & github copilot chat is installed as vscode extension

- install & verify rest-client is installed as vscode extension

- install & verify rust-analyzer is installed as vscode extension

# Setup Workspace

- create & change hello-world directory
```
mkdir hello-world && cd hello-world
```

- convert hello-world directory into repository
```
git init
```

- create .gitignore file
```
cat > .gitignore<<EOF
target
.vscode
.history
EOF
```

- create Cargo.toml file
```
cat > Cargo.toml<<EOF
[workspace]
resolver = "2"

members = [
    hello_main
]
EOF
```

- create hello_main project & change directory
```
cargo new hello_main && cd hello_main
```

- check hello_main crate
```
cargo check
```

- compile hello_main crate
```
cargo build

../target/debug/hello_main
```

- execute hello_main crate
```
cargo run
```

# Setup Webserver

- add dependencies
```
cargo add axum
cargo add serde --features derive
cargo add serde_json
cargo add tokio --features full
cargo add uuid --features serde,v4
```

# Setup Rest-Client

- create hello_main/.env file 
```
cat > .env<<EOF
HOSTADDR=127.0.0.1
HOSTPORT=3000
EOF
```

- create hello_main.http file
```
cat > hello_main.http<<EOF
@host = {{$dotenv HOSTADDR}}:{{$dotenv HOSTPORT}}
###

# @name create_item
POST http://{{host}}/items HTTP/1.1
content-type: application/json

{
  "name": "item1",
  "value": "This is item 1"
}
###

# @name select_items
GET http://{{host}}/items HTTP/1.1
###

@itemId = {{select_items.response.body.$[0].id}}

# @name update_item
PUT http://{{host}}/items/{{itemId}} HTTP/1.1
content-type: application/json

{
  "name": "item2",
  "value": "This is the updated item 2"
}
###

# @name select_item
GET http://{{host}}/items/{{itemId}} HTTP/1.1
###

# @name delete_item
DELETE http://{{host}}/items/{{itemId}} HTTP/1.1
###

EOF
```

# Setup Validation

- add validator dependency
```
cargo add validator --features derive
```

- import Validate trait & ValidationErrors struct from validator crate
```
use validator::{Validate, ValidationErrors};
```

- add validation_errors_to_map fn
```
fn validation_errors_to_map(errors: ValidationErrors) -> serde_json::Value {...}
```

- annotate name & value fields in CreateItem and UpdateItem structs
```
#[validate(length(min = 1, message = "field is empty"))]
```

# Setup Tracing

- add tracing dependencies
```
cargo add tracing
cargo add tracing-subscriber
```

- import info, debug & warn functions from tracing crate
```
use tracing::{info, debug, warn};
```

- initialize tracing subscriber in main function before starting the server
```
tracing_subscriber::fmt::init();
```

- add tracing spans or events to handlers for debugging and observability
```
tracing::info!("Creating item: {:?}", payload.name);
tracing::debug!(?params, "Filtering items");
tracing::warn!("Item not found: {}", id);
```

# Setup Tests