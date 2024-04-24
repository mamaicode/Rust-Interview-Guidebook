### 1. Whats the difference between String vs &str?

### 2. What is a borrow checker in Rust?

### 3. Does Rust have a standard async runtime?

### 4. Explain the following code:
#[stable(feature = "rust1", since = "1.0.0")]
pub fn spawn<F, T>(f: F) -> JoinHandle<T>
where
    F: FnOnce() -> T,
    F: Send + 'static,
    T: Send + 'static,
{
    Builder::new().spawn(f).expect("failed, cannot spawn the thread")
}

### 6. What can you do in unsafe block?

### 7. Dereference a raw pointer:
let original: u32 = 23;
let ptr = &original as *const u32;
### dereference a raw pointer on this line

println!("{value}");

### 8. Describe dynamic vs static dispatch

### 9. There have been deadlocks noticed in a component of a service. How would you reduce/remove the possibility of deadlock?

### 10. What are macros? What kind of macros are there and what are the differences?

### 11. Write a macro where it takes () and returns some number X
macro_rules! give_six {
    () => {
        6
    };
}

fn main() {
    let six = give_six!();
    println!("{}", six);
}

### 12. Can you create Macro with Arguments?
### A macro named `print_message`
macro_rules! print_message {
    ### Match rule that takes an argument expression
    ($message:expr) => {
        println!("{}", $message)
    };
}

fn main() {
    ### Call the macro with an argument
    print_message!("I am learning Rust!");
}

### 13. Is the following code safe? If not what would you change?
use bumpalo::Bump;

pub type TokenVec<'s> = Vec<Token, &'s Bump>;

### We want the [`Lexer`] to own the allocator because this is an internal
### design that should not be exposed to the users.
#[repr(C)]
pub struct Lexer<'s> {
    model: LexerModel<'s>,
    allocator: Bump,
}

impl<'s> Lexer<'s> {
    pub fn new(input: &'s str) -> Self {
        let capacity = input.len() / AVERAGE_TOKEN_LEN;
        let allocator = Bump::with_capacity(capacity);
        let allocator_ref: &Bump = unsafe { &*(&allocator as *const Bump) };
        let model = LexerModel::new(allocator_ref, &input);
        Self { allocator, model }
    }
}

pub struct LexerModel<'s> {
    pub input: &'s str,
    pub iterator: std::str::CharIndices<'s>,
    pub output: TokenVec<'s>,
    pub state: ModelState
}

impl<'s> LexerModel<'s> {
    pub fn new(allocator_ref: &'s Bump, input: &'s str) -> Self {
        let iterator = input.char_indices();
        let output = Vec::new_in(allocator_ref);
        let state = Default::default();
        Self {input, iterator, output, state}
    }
}

### 14. Describe the Send and Sync traits in Rust and explain their significance in concurrent programming.

### 15. How do you estimate coding tasks? What do you do if you misjudge your task and you are late?

### 16. What can you build with Tokio/Why would you use Tokio?

### 17. What is the difference between threads and async programming?

### 18. What's the difference between "impl Trait" vs "T: Trait" vs "Box<dyn Trait>" (in function return position)?

### 19. What happens if I run this code:
fn main() {
    let mut owned_string: String = "hello ".to_owned();
    let borrowed_string: &str = "world";

    owned_string.push_str(borrowed_string);
    println!("{}", owned_string);
}
### So what can we say about the difference between String and Str?

### 22. Have you used Docker for development?

### 23. Write an example of struct, enum, impl, and trait

### 24. Can we println!("x={}", x); in this code:
fn main() {
    let x = String::from("Hi there! I'm irine");
    let y = x;

    println!...
}

### 25. What is the output of this code?
async fn say_world() {
    println!("world");
}

#[tokio::main]
async fn main() {
    let op = say_world();
    println!("hello");
    op.await;
}

### 26. What is serde?

### 27. Finish the commented section
use serde_json::{Result, Value};

fn untyped_example() -> Result<()> {
    ### Some JSON input data as a &str. Maybe this comes from the user.
    let data = r#"
        {
            "name": "John Doe",
            "age": 43,
            "phones": [
                "+44 1234567",
                "+44 2345678"
            ]
        }"#;

    ### Parse the string of data into serde_json::Value.

    ### Access parts of the data by indexing with square brackets

}

28. Consider the following code:

###/ An empty generic representation of a struct. It represents a struct with no fields.
pub struct Empty;

###/ A generic representation of a struct with many fields. For example, a struct can be
###/ represented as `Field<usize, Field<String, Empty>>`.
pub struct Field<FirstField, RestOfFields>(pub FirstField, pub RestOfFields);

###/ Trait for any structure that can be converted to generic representation.
###/ For example, generic representation of `(usize, String)` is `Field<usize, Field<String, Empty>>`.
pub trait HasGenericRepr {
    type GenericRepr;
}

pub type GenericRepr<T> = <T as HasGenericRepr>::GenericRepr;

###/ Trait allowing converting any struct to its generic representation.
pub trait IntoGenericRepr: HasGenericRepr {
    fn into_generic_repr(self) -> GenericRepr<Self>;
}

Implement the following things:

    Ability to convert tuples of any types to their generic representation.
    Method first_field_ref which will return the reference to the first field of any generic representation and any structure that implements IntoGenericRepr. For example, it should be possible to call (1, "hello").first_field(), and Field(1, Field("hello", Empty)).first_field(), which should return &1.
    Method map which will allow mapping any function on all fields of a struct and return the struct itself. For example, it should be possible to call (1, "hello").map(...) with a function format!("{t}!") to get ("1!", "hello!"). How the function is passed to the map method is up to you (it can be passed as a closure, as a structure representing that function, etc.).


29. Explain the following code and explain what CORS are:

{
    let link_repository = LinksRepository {
        db_connection: db_conn.clone(),
    };

    let state = AppState { link_repository };

    HttpServer::new(move || {
        let cors = Cors::default()
            .allow_any_origin()
            .send_wildcard()
            .allowed_headers(vec!["content-type", "authorization"])
            .allowed_methods(vec!["GET", "POST", "PUT", "DELETE"])
            .max_age(3600);

        App::new()
            .app_data(Data::new(state.clone()))
            .wrap(cors)
            .wrap(Logger::default())
            .service(get_all)
            .service(save_link)
    })
    .bind(("127.0.0.0", 8080))?
    .run()
    .await
}

