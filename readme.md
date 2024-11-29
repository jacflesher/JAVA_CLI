
### Step 1: Run Your Java Application in Debug Mode
You need to start your Java application in **debug mode** so it can listen for debugger connections.

1. **Set JVM options for remote debugging** when running the application. These options enable the Java Debug Wire Protocol (JDWP), which allows you to connect a debugger to your application. You can do this with `gradlew` or by directly running `java`.

For example, use the following Gradle task or command to start the application with debugging enabled:

#### Option A: Using Gradle (`gradlew`)
Add the JVM arguments to the `runDebug` task in `build.gradle`:

```groovy
task runDebug(type: JavaExec) {
    main = 'com.example.Main'  // Replace with your main class
    classpath = sourceSets.main.runtimeClasspath
    jvmArgs = [
        '-Xdebug',
        '-Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005'
    ]
}
```

Run it via the command line:

```bash
./gradlew runDebug
```

This will start the Java application and listen for a debugger to connect on port `5005`. The `suspend=n` option means the application will **not pause** at startup and will continue running until the debugger attaches.

#### Option B: Using `java` Directly
If you prefer to run the application directly with `java`, you can use the following command:

```bash
java -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005 -cp your-classpath com.example.Main
```

This command runs the application and listens for debugger connections on port `5005`.

### Step 2: Connect to the Java Application with `jdb`
Once the Java application is running in debug mode, you can use **`jdb`** (Java Debugger) to connect to it and set breakpoints. `jdb` is a command-line tool that comes with the JDK, and it allows you to interact with the JVM and set breakpoints, inspect variables, and step through code.

1. Open a new terminal window.
2. Use `jdb` to connect to the Java application. If your application is running locally on port `5005`, use the following command:

```bash
jdb -attach localhost:5005
```

This connects `jdb` to the JVM running your application on port `5005`.

### Step 3: Set Breakpoints and Debug Using `jdb`
Once you’re connected to the JVM with `jdb`, you can set breakpoints and control the execution of the application.

1. **Set a breakpoint** at a specific line of code:

   ```bash
   stop in com.example.Main.myMethod
   ```

   This sets a breakpoint in the `myMethod` method of the `com.example.Main` class.

   Alternatively, you can set a breakpoint at a specific line number in a class:

   ```bash
   stop at com.example.Main:25
   ```

   This sets a breakpoint at line 25 in the `com.example.Main` class.

2. **Run the application** from the point where it paused (the application will pause execution when it hits the breakpoint):

   ```bash
   cont
   ```

3. **Step through the code**:
   - **Step over** the current line: 

     ```bash
     next
     ```

   - **Step into** a method (i.e., go into the method’s code):

     ```bash
     step
     ```

   - **Step out** of the current method:

     ```bash
     step up
     ```

4. **Inspect variables**: You can print out the value of variables at any time using the `print` command. For example, to inspect a variable called `myVar`, use:

   ```bash
   print myVar
   ```

5. **Resume execution** until the next breakpoint is hit:

   ```bash
   cont
   ```

6. **Exit `jdb`** when done:

   ```bash
   quit
   ```

### Example Debugging Session with `jdb`
Here’s what a simple session with `jdb` might look like:

```bash
# Start the debugger and attach it to the running Java process
$ jdb -attach localhost:5005

Initializing jdb ...
>
# Set a breakpoint at the start of a method
> stop in com.example.Main.myMethod

Deferring breakpoint com.example.Main.myMethod.
It will be set after the class is loaded.

> cont
> 
# The application runs, and it will stop at the breakpoint

# Now, you can step through the code:
> step  # Step into the next method
> next  # Step over the current line

# Inspect a variable
> print myVar

# Continue running until the next breakpoint or completion
> cont

# Exit jdb when finished
> quit
```

### Summary of `jdb` Commands:
- **`stop in`**: Set a breakpoint at the beginning of a method.
- **`stop at`**: Set a breakpoint at a specific line number.
- **`cont`**: Continue execution until the next breakpoint.
- **`next`**: Step over the current line.
- **`step`**: Step into the current method.
- **`step up`**: Step out of the current method.
- **`print`**: Print the value of a variable or expression.
- **`quit`**: Exit the debugger.

### Step 4: Debugging Workflow
Once you've attached `jdb` to your Java application, you'll have the full ability to inspect and control the application as it runs. You can set multiple breakpoints, step through the code, and examine variables, all through the command line. This is a completely **IDE-agnostic** setup for debugging Java applications.

### Additional Notes:
- **Alternative Debuggers**: While `jdb` is the standard Java CLI debugger, other debuggers such as **VisualVM** or **Eclim** (a command-line interface for Eclipse) could also be used, but they are typically more feature-rich and rely on GUI components.
- **Using Gradle for Debugging**: Gradle's ability to run tasks in debug mode with `gradlew` makes it easy to integrate into a build automation pipeline. However, `jdb` remains the simplest, most direct CLI-based debugger for Java.
