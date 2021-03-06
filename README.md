# Notify

[![Crate version](https://img.shields.io/crates/v/notify.svg?style=flat-square)](https://crates.io/crates/notify)
[![Crate license](https://img.shields.io/crates/l/notify.svg?style=flat-square)](https://creativecommons.org/publicdomain/zero/1.0/)
![Crate download count](https://img.shields.io/crates/d/notify.svg?style=flat-square)

[![Appveyor](https://img.shields.io/appveyor/ci/passcod/rsnotify.svg?style=flat-square)](https://ci.appveyor.com/project/passcod/rsnotify) <sup>(Windows)</sup>
[![Travis](https://img.shields.io/travis/passcod/rsnotify.svg?style=flat-square)](https://travis-ci.org/passcod/rsnotify) <sup>(Linux and OS X)</sup>

[![Code of Conduct](https://img.shields.io/badge/contributor-covenant-123456.svg?style=flat-square)](http://contributor-covenant.org/version/1/3/0/)


_Cross-platform filesystem notification library for Rust._

## Install

```toml
[dependencies]
notify = "^2.5.0"
```

## Usage

```rust
extern crate notify;

use notify::{RecommendedWatcher, Error, Watcher};
use std::sync::mpsc::channel;

fn main() {
  // Create a channel to receive the events.
  let (tx, rx) = channel();

  // Automatically select the best implementation for your platform.
  // You can also access each implementation directly e.g. INotifyWatcher.
  let w: Result<RecommendedWatcher, Error> = Watcher::new(tx);

  match w {
    Ok(mut watcher) => {
      // Add a path to be watched. All files and directories at that path and
      // below will be monitored for changes.
      watcher.watch("/home/test/notify");

      // You'll probably want to do that in a loop. The type to match for is
      // notify::Event, look at src/lib.rs for details.
      match rx.recv() {
        _ => println!("Recv.")
      }
    },
    Err(_) => println!("Error")
  }
}
```

## Platforms

- Linux / Android: inotify
- OS X: FSEvent
- Windows: ReadDirectoryChangesW
- All platforms: polling

## Limitations

### FSEvent

Due to the inner security model of FSEvent (see [FileSystemEventSecurity](https://developer.apple.com/library/mac/documentation/Darwin/Conceptual/FSEvents_ProgGuide/FileSystemEventSecurity/FileSystemEventSecurity.html)), some event cannot be observed easily when trying to follow files that do not belong to you. In this case, reverting to the pollwatcher can fix the issue, with a slight performance cost.

## Todo

- BSD / OS X / iOS: kqueue
- Solaris 11: FEN

Pull requests and bug reports happily accepted!

## Origins

Inspired by Go's [fsnotify](https://github.com/go-fsnotify/fsnotify), born out
of need for [cargo watch](https://github.com/passcod/cargo-watch), and general
frustration at the non-existence of C/Rust cross-platform notify libraries.

Written by [Félix Saparelli](https://passcod.name) and awesome
[contributors](https://github.com/passcod/rsnotify/graphs/contributors),
and released in the Public Domain using the Creative Commons Zero Declaration.
