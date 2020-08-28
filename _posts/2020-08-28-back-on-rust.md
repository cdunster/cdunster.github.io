---
title: "Back on Rust with defmt!()"
date: 2020-08-28
summary: "Back again with more Rust development."
tags: [development, rust]
---

After creating some simple apps in Flutter it is time for me to go back to Rust.

I really enjoyed working with Flutter and I'm proud of the apps that I made in such a short time, although nothing of worth they were still fun to make. I will likely go back to Flutter in the future.

Lately Rust seems to be growing in popularity, especially in embedded systems, so now I want to go back to it to experiment with all the new toys: the thing that excites me the most is the [Knurling project][knurling] from Ferrous Systems their plan is to make running embedded Rust projects just as easy as other Rust projects they're adding a bunch of tools to make this happen. They also have an interesting crate [defmt][defmt] which is like a semi-hosted logging system which allows logs from embedded systems to be do in real-time without having to halt the CPU. From what I understand they do this by storing the strings on the host in a sort of dictionary so the target can then just send the key for the string and the host looks it up in the dictionary and prints that string. e.g. `defmt!("Hello, World!");` would just require the target to send the key of the string and some metadata, totalling a few bytes, over the RTT (other mediums are in the works) and the host would print the full string. This works for any length of string as well as formatted strings with values so `defmt!("Value: {:u8}", value);` would now just require the string key, value, and the metadata. This all means that the target doesn't have to spend cycles formatting a string and then sending blocks of data over the RTT and instead it can just send a few bytes straight over the RTT meaning that the CPU doesn't need to be halted which could result in weird timing issues when you disable logging.

[knurling]: https://ferrous-systems.com/blog/knurling-rs
[defmt]: https://ferrous-systems.com/blog/defmt