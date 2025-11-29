tpgn
![Uploading Screenshot_20251105-115922_Free Fire.jpg…]()
![268f0ab5796d70464e0d05bbe765b0eb](https://github.com/user-attachments/assets/cedddb1b-8519-413e-b967-a0e570ed7cb0)
![ffcc96f8dca5934d2a3688c683fe14b5](https://github.com/user-attachments/assets/87fb536c-3f5d-4fa3-bc0c-ca6904fd4c95)
![035be116cf75a0345f004e34f8c2a745](https://github.com/user-attachments/assets/7c2ac8a4-5211-4c5d-a0ec-26fd5ff55afa)
![d43a73f4b6895cf5a9abf13041e5d3a3](https://github.com/user-attachments/assets/ba73d6bc-d064-48ab-9854-b4fef82fb8e4)
# Introduction

Shizuku can help normal apps uses system APIs directly with adb/root privileges with a Java process started with app_process.

The name Shizuku comes from [a character](https://danbooru.donmai.us/posts/3553474).

## Why was Shizuku born?

The birth of Shizuku has two main purposes.

1. Provide a convenient way to use system APIs
2. Convenient for the development of some apps that only requires adb permissions

## Shizuku vs. "Old school" method

### "Old school" method

For example, to enable/disable components, some apps that require root privileges execute `pm disable` directly in `su`.

1. Execute `su`
2. Execute `pm disable`
3. (pre-Pie) Start the Java process with app_process ([see here](https://android.googlesource.com/platform/frameworks/base/+/oreo-release/cmds/pm/pm))
4. (Pie+) Execute the native program `cmd` ([see here](https://android.googlesource.com/platform/frameworks/native/+/pie-release/cmds/cmd/))
5. Process the parameters, interact with the system server through the binder, and process the result to output the text result.

Each of the "Execute" means a new process creation, su internally uses sockets to interact with the su daemon, and a lot of time and performance are consumed in such process. (Some poorly designed app will even execute `su` **every time** for each command)

The disadvantages of this type of method are:

1. **Extremely slow**
2. Need to process the text to get the result
3. Features are subject to available commands
4. Even if adb has sufficient permissions, the app requires root privileges to run

### Shizuku method

The Shizuku app will direct the user to run a process (Shizuku service process) using root or adb.

1. When the app process starts, the Shizuku service process sends the binder to the app process.
2. The app interacts with the Shizuku service through the binder, and the Shizuku service process interacts with the system server through the binder.

The advantages of Shizuku are:

1. Minimal extra time and performance consumption
2. It is almost identical to the direct invocation API experience (app developers only need to add a small amount of code)
