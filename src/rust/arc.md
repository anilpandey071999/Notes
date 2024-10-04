# ARC

- Arc ([Atomically Reference Counted](https://doc.rust-lang.org/std/sync/struct.Arc.html))
  - Arc provide shared ownership of value.
  - Allocation is done in the heap.
  - Invoking clone on Arc produces a new Arc instance Which points to same address location of the value and increase the reference count.
  - If the last Arc pointer allocation is destroyed, the value allocation will be dropped.
  - Shared reference in rust disallow mutation by default and Arc is no exception.
  - ONLY way to make it mutable by using mutex, RwLock or Atomic types
  - ### **Thread Safety of Arc**


    - if data is not sending between threads then it better to use RC for lower overhead.
    - Arc implements `send` and `sync` as long T implement `send` and `sync`.
    - Arc doesn't provide thread safety to it data.
    - It's only allow to have multiple owner of arc's data.
    - In the end, this means that you may need to pair `Arc<T>` with some sort of [`std::sync`](https://doc.rust-lang.org/std/sync/index.html) type, usually [`Mutex<T>`](https://doc.rust-lang.org/std/sync/struct.Mutex.html).
  - ### **Deref behavior**


    - Arc automatically deref to T
    - To avoid name clashes with `T`’s methods, the methods of `Arc<T>` itself are associated functions, called using [fully qualified syntax](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#fully-qualified-syntax-for-disambiguation-calling-methods-with-the-same-name)
    - ```
      use std::sync::Arc;

      let my_arc = Arc::new(());
      let my_weak = Arc::downgrade(&my_arc);
      ```
  - ### Examples

    - Sharing some immutable data between threads:
        ```
        use std::sync::Arc;
        use std::thread;

        let five = Arc::new(5);

        for _ in 0..10 {
            let five = Arc::clone(&five);

            thread::spawn(move || {
                println!("{five:?}");
            });
        }
        ```

        - Sharing a mutable [`AtomicUsize`](https://doc.rust-lang.org/std/sync/atomic/struct.AtomicUsize.html "sync::atomic::AtomicUsize"):

        ```
        use std::sync::Arc;
        use std::sync::atomic::{AtomicUsize, Ordering};
        use std::thread;

        let val = Arc::new(AtomicUsize::new(5));

        for _ in 0..10 {
            let val = Arc::clone(&val);

            thread::spawn(move || {
                let v = val.fetch_add(1, Ordering::Relaxed);
                println!("{v:?}");
            });
        }
        ```
    
        - See the [`rc` documentation](https://doc.rust-lang.org/std/rc/index.html#examples "mod std::rc") for more examples of reference counting in general.
