# AGENT 2 – JAVA 17 UPLIFT ENGINE  
**Objective:**  
Transform all Java source code from Java 8 → Java 17 syntax and APIs **while preserving business logic.**

---

## 🔒 STRICT RULES
- Read files ONLY from `legacy_repo/`
- Write transformed files ONLY to:  
  `migrated_code/lifting_java17/`
- DO NOT modify original files
- DO NOT perform any microservice changes  
- DO NOT delete any files  
- Preserve all business logic
- **ALWAYS use Gradle for build configuration (NOT Maven)**

---

## 📌 COMPREHENSIVE JAVA 17 MIGRATION PLAYBOOK

Perform the following transformations for every `.java` file, applying all applicable modernizations:

### 1️⃣ COLLECTIONS MODERNIZATION

#### Legacy Collections → Modern Alternatives
```java
// BEFORE (Java 8)
Vector<String> vector = new Vector<>();
Hashtable<String, Integer> table = new Hashtable<>();
Stack<Integer> stack = new Stack<>();

// AFTER (Java 17)
List<String> list = new ArrayList<>(); // or Collections.synchronizedList() if thread-safety needed
Map<String, Integer> map = new ConcurrentHashMap<>(); // for concurrent access
Deque<Integer> deque = new ArrayDeque<>(); // use Deque interface
```

#### Collection Factory Methods (Java 9+)
```java
// BEFORE
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
Set<Integer> set = new HashSet<>(Arrays.asList(1, 2, 3));
Map<String, Integer> map = new HashMap<>();
map.put("key1", 1);

// AFTER
List<String> list = List.of("a", "b"); // Immutable
Set<Integer> set = Set.of(1, 2, 3); // Immutable
Map<String, Integer> map = Map.of("key1", 1, "key2", 2); // Immutable

// For mutable collections:
List<String> mutableList = new ArrayList<>(List.of("a", "b"));
```

### 2️⃣ TRY-WITH-RESOURCES ENHANCEMENTS

#### Automatic Resource Management
```java
// BEFORE (Java 7 style)
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("file.txt"));
    String line = reader.readLine();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

// AFTER (Java 17 - try-with-resources)
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line = reader.readLine();
} catch (IOException e) {
    e.printStackTrace();
}

// AFTER (Java 9+ - try-with-resources with effectively final variables)
BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
try (reader) {
    String line = reader.readLine();
}
```

#### Multiple Resources
```java
// AFTER (Java 17)
try (FileInputStream fis = new FileInputStream("input.txt");
     FileOutputStream fos = new FileOutputStream("output.txt");
     BufferedReader reader = new BufferedReader(new InputStreamReader(fis));
     BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(fos))) {
    String line;
    while ((line = reader.readLine()) != null) {
        writer.write(line);
    }
}
```

### 3️⃣ TEXT BLOCKS (Java 13+)

```java
// BEFORE
String json = "{\n" +
              "  \"name\": \"John\",\n" +
              "  \"age\": 30\n" +
              "}";

String sql = "SELECT id, name, email " +
             "FROM users " +
             "WHERE status = 'active' " +
             "ORDER BY name";

// AFTER
String json = """
    {
      "name": "John",
      "age": 30
    }
    """;

String sql = """
    SELECT id, name, email
    FROM users
    WHERE status = 'active'
    ORDER BY name
    """;
```

### 4️⃣ RECORDS (Java 14+)

#### Replace DTOs/POJOs with Records
```java
// BEFORE
public class Person {
    private final String name;
    private final int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    
    @Override
    public boolean equals(Object o) { /* boilerplate */ }
    @Override
    public int hashCode() { /* boilerplate */ }
    @Override
    public String toString() { /* boilerplate */ }
}

// AFTER
public record Person(String name, int age) {
    // Compact constructor for validation
    public Person {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}

// Use ONLY for:
// - DTOs (Data Transfer Objects)
// - Value Objects
// - Pure data carriers
// DO NOT use for entities with business logic
```

### 5️⃣ SEALED CLASSES (Java 17)

```java
// Define restricted inheritance hierarchies
public sealed interface Shape permits Circle, Rectangle, Triangle {
    double area();
}

public final class Circle implements Shape {
    private final double radius;
    public Circle(double radius) { this.radius = radius; }
    public double area() { return Math.PI * radius * radius; }
}

public final class Rectangle implements Shape {
    private final double width, height;
    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
    public double area() { return width * height; }
}

public non-sealed class Triangle implements Shape {
    // Can be extended further
    public double area() { /* implementation */ return 0; }
}
```

### 6️⃣ PATTERN MATCHING

#### Pattern Matching for instanceof (Java 16+ - FINALIZED)
```java
// BEFORE
if (obj instanceof String) {
    String str = (String) obj;
    System.out.println(str.length());
}

// AFTER (Java 16+ - Safe to use, finalized in JEP 394)
if (obj instanceof String str) {
    System.out.println(str.length());
}

// With complex conditions
if (obj instanceof String str && str.length() > 5) {
    System.out.println("Long string: " + str);
}
```

#### Pattern Matching for switch (Java 17 PREVIEW - Use with caution)
⚠️ **NOTE:** This is a PREVIEW feature in Java 17. Requires `--enable-preview` flag (included in Gradle configuration below).
Only use if preview features are acceptable in your environment.

```java
// BEFORE
String formatted;
if (obj instanceof Integer) {
    formatted = String.format("int %d", (Integer) obj);
} else if (obj instanceof Long) {
    formatted = String.format("long %d", (Long) obj);
} else if (obj instanceof Double) {
    formatted = String.format("double %f", (Double) obj);
} else {
    formatted = obj.toString();
}

// AFTER (Java 17+ with preview features enabled)
String formatted = switch (obj) {
    case Integer i -> String.format("int %d", i);
    case Long l -> String.format("long %d", l);
    case Double d -> String.format("double %f", d);
    default -> obj.toString();
};
```

### 7️⃣ ENHANCED SWITCH EXPRESSIONS (Java 14+)

```java
// BEFORE
String result;
switch (day) {
    case MONDAY:
    case FRIDAY:
    case SUNDAY:
        result = "6 letters";
        break;
    case TUESDAY:
        result = "7 letters";
        break;
    default:
        result = "Other";
        break;
}

// AFTER
String result = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> "6 letters";
    case TUESDAY -> "7 letters";
    default -> "Other";
};

// With yield for complex logic
int numLetters = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> {
        System.out.println("6 letter day");
        yield 6;
    }
    case TUESDAY -> {
        System.out.println("7 letter day");
        yield 7;
    }
    default -> {
        System.out.println("Other day");
        yield -1;
    }
};
```

### 8️⃣ VAR KEYWORD (Java 10+)

```java
// Use var for improved readability with obvious types
// BEFORE
Map<String, List<Customer>> customersByCity = new HashMap<>();
BufferedReader reader = new BufferedReader(new FileReader("file.txt"));

// AFTER
var customersByCity = new HashMap<String, List<Customer>>();
var reader = new BufferedReader(new FileReader("file.txt"));

// DO NOT use var when type is not obvious:
// BAD: var result = calculate(); // What type is this?
// GOOD: Customer customer = fetchCustomer();

// Use var in loops
for (var entry : map.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

try (var inputStream = new FileInputStream("file.txt")) {
    // use inputStream
}
```

### 9️⃣ STREAM API ENHANCEMENTS

```java
// takeWhile, dropWhile (Java 9+)
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8);
List<Integer> lessThan5 = numbers.stream()
    .takeWhile(n -> n < 5)
    .collect(Collectors.toList());

List<Integer> from5 = numbers.stream()
    .dropWhile(n -> n < 5)
    .collect(Collectors.toList());

// iterate with predicate (Java 9+)
Stream.iterate(0, n -> n < 100, n -> n + 1)
    .forEach(System.out::println);

// ofNullable (Java 9+)
Stream<String> stream = Stream.ofNullable(possiblyNullValue);

// Collectors.teeing (Java 12+)
var result = stream.collect(Collectors.teeing(
    Collectors.summingInt(Integer::intValue),
    Collectors.counting(),
    (sum, count) -> sum / count
));

// toList() (Java 16+)
List<String> list = stream.filter(s -> s.length() > 3)
    .map(String::toUpperCase)
    .toList(); // Returns immutable list
```

### 🔟 OPTIONAL ENHANCEMENTS

```java
// or() method (Java 9+)
Optional<String> result = optional.or(() -> Optional.of("default"));

// ifPresentOrElse() (Java 9+)
optional.ifPresentOrElse(
    value -> System.out.println("Found: " + value),
    () -> System.out.println("Not found")
);

// stream() (Java 9+)
List<String> results = listOfOptionals.stream()
    .flatMap(Optional::stream)
    .collect(Collectors.toList());

// isEmpty() (Java 11+)
if (optional.isEmpty()) {
    // handle empty case
}
```

### 1️⃣1️⃣ STRING ENHANCEMENTS (Java 11+)

```java
// isBlank() - checks if string is empty or contains only whitespace
if (!str.isBlank()) {
    // process non-blank string
}

// lines() - stream of lines
str.lines()
    .filter(line -> !line.isBlank())
    .forEach(System.out::println);

// strip(), stripLeading(), stripTrailing() - better than trim()
String trimmed = str.strip(); // removes Unicode whitespace
String leading = str.stripLeading();
String trailing = str.stripTrailing();

// repeat() (Java 11+)
String repeated = "abc".repeat(3); // "abcabcabc"

// indent() (Java 12+)
String indented = "line1\nline2".indent(4);

// transform() (Java 12+)
String result = str.transform(s -> s.strip())
    .transform(String::toLowerCase);
```

### 1️⃣2️⃣ DEPRECATED API REPLACEMENTS

#### Date/Time API
```java
// BEFORE
Date date = new Date();
Calendar calendar = Calendar.getInstance();
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");

// AFTER
LocalDate date = LocalDate.now();
LocalDateTime dateTime = LocalDateTime.now();
ZonedDateTime zonedDateTime = ZonedDateTime.now();
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");

// Conversions
Instant instant = Instant.now();
LocalDate dateFromInstant = instant.atZone(ZoneId.systemDefault()).toLocalDate();
```

#### HttpClient (Java 11+)
```java
// BEFORE
URL url = new URL("https://api.example.com/data");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");
BufferedReader reader = new BufferedReader(
    new InputStreamReader(conn.getInputStream()));

// AFTER
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/data"))
    .GET()
    .build();

HttpResponse<String> response = client.send(request, 
    HttpResponse.BodyHandlers.ofString());
String body = response.body();

// Async
client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
    .thenApply(HttpResponse::body)
    .thenAccept(System.out::println);
```

#### Files API
```java
// BEFORE
File file = new File("path/to/file.txt");
FileReader fr = new FileReader(file);
BufferedReader br = new BufferedReader(fr);

// AFTER
Path path = Path.of("path/to/file.txt");
List<String> lines = Files.readAllLines(path);
String content = Files.readString(path);
Files.writeString(path, "content");

// Stream lines
try (Stream<String> stream = Files.lines(path)) {
    stream.forEach(System.out::println);
}
```

### 1️⃣3️⃣ NULLPOINTEREXCEPTION IMPROVEMENTS (Java 14+)

```java
// Java 14+ provides helpful NPE messages showing which variable was null
// Example: Cannot invoke "String.length()" because "str" is null

// Use Objects utility methods
Objects.requireNonNull(value, "Value cannot be null");
String result = Objects.requireNonNullElse(value, "default");
String result2 = Objects.requireNonNullElseGet(value, () -> computeDefault());
```

### 1️⃣4️⃣ LAMBDAS & METHOD REFERENCES

```java
// BEFORE - Anonymous inner classes
list.sort(new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});

button.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println("Button clicked");
    }
});

// AFTER - Lambdas
list.sort((s1, s2) -> s1.compareTo(s2));
// Or method reference
list.sort(String::compareTo);

button.addActionListener(e -> System.out.println("Button clicked"));

// Constructor references
List<String> strings = List.of("1", "2", "3");
List<Integer> integers = strings.stream()
    .map(Integer::new)
    .collect(Collectors.toList());
```

### 1️⃣5️⃣ CONCURRENCY IMPROVEMENTS

```java
// CompletableFuture enhancements (Java 9+)
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello")
    .orTimeout(1, TimeUnit.SECONDS)
    .completeOnTimeout("default", 1, TimeUnit.SECONDS);

// Process API (Java 9+)
ProcessHandle currentProcess = ProcessHandle.current();
System.out.println("PID: " + currentProcess.pid());

// Flow API (Reactive Streams) (Java 9+)
// For reactive programming with backpressure
```

### 1️⃣6️⃣ PERFORMANCE OPTIMIZATIONS

```java
// Compact Number Format (Java 12+)
NumberFormat fmt = NumberFormat.getCompactNumberInstance(
    Locale.US, NumberFormat.Style.SHORT);
String formatted = fmt.format(1000); // "1K"

// String.formatted() (Java 15+)
String message = "Hello, %s! You have %d messages.".formatted(name, count);

// Use Arrays.compare() and Arrays.mismatch()
int result = Arrays.compare(arr1, arr2);
int firstDiff = Arrays.mismatch(arr1, arr2);
```

---

## 🏗️ GRADLE BUILD CONFIGURATION

### Build File Setup (build.gradle or build.gradle.kts)

**ALWAYS create/update Gradle build files, NOT Maven pom.xml**

#### build.gradle (Groovy)
```gradle
plugins {
    id 'java'
    id 'application'
}

group = 'com.example'
version = '1.0-SNAPSHOT'

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter:3.2.0'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

tasks.named('test') {
    useJUnitPlatform()
}

// ONLY include --enable-preview if using preview features (e.g., pattern matching for switch)
// Remove or comment out if not using preview features
compileJava {
    options.compilerArgs += [
        // '--enable-preview', // UNCOMMENT ONLY if using preview features like pattern matching for switch (JEP 406)
        '-Xlint:unchecked',
        '-Xlint:deprecation'
    ]
}

// If using preview features, also add to test and run tasks:
// tasks.withType(JavaExec) {
//     jvmArgs += ['--enable-preview']
// }
// tasks.named('test') {
//     jvmArgs += ['--enable-preview']
// }
```

#### build.gradle.kts (Kotlin DSL)
```kotlin
plugins {
    java
    application
}

group = "com.example"
version = "1.0-SNAPSHOT"

java {
    toolchain {
        languageVersion.set(JavaLanguageVersion.of(17))
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter:3.2.0")
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.0")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}

tasks.test {
    useJUnitPlatform()
}

// ONLY include --enable-preview if using preview features (e.g., pattern matching for switch)
// Remove or comment out if not using preview features
tasks.withType<JavaCompile> {
    options.compilerArgs.addAll(listOf(
        // "--enable-preview", // UNCOMMENT ONLY if using preview features like pattern matching for switch (JEP 406)
        "-Xlint:unchecked",
        "-Xlint:deprecation"
    ))
}

// If using preview features, also add to test and run tasks:
// tasks.withType<JavaExec> {
//     jvmArgs("--enable-preview")
// }
// tasks.test {
//     jvmArgs("--enable-preview")
// }
```

#### settings.gradle
```gradle
rootProject.name = 'migrated-app'
```

### Gradle Wrapper Setup
```bash
# Include Gradle wrapper files in migrated code
gradle wrapper --gradle-version 8.5
```

**Include these files in output:**
- `gradlew` (Unix executable)
- `gradlew.bat` (Windows executable)
- `gradle/wrapper/gradle-wrapper.jar`
- `gradle/wrapper/gradle-wrapper.properties`

---

## 📋 ARCHITECTURE CONSTRAINTS

### DO NOT CHANGE:
- No re-architecture  
- No Spring Boot changes  
- No microservice decomposition  
- No new endpoints
- No business logic alterations

### ONLY CHANGE:
- Java syntax and APIs
- Build configuration (Maven → Gradle)
- Deprecated API usage
- Code style and patterns

---

## 🔍 FILE PROCESSING WORKFLOW

For EVERY `.java` file in `legacy_repo/`:

1. **Read** the source file
2. **Analyze** for applicable transformations:
   - Collections usage
   - Resource management
   - String operations
   - Date/Time API usage
   - Null checks
   - Anonymous classes
   - Switch statements
   - Instance checks
   - DTO/POJO patterns

3. **Apply** transformations (in order of safety):
   - Deprecated API replacements (MUST DO)
   - Try-with-resources (MUST DO for AutoCloseable)
   - Collection factory methods (SAFE)
   - String enhancements (SAFE)
   - Lambda conversions (SAFE)
   - var keyword (SAFE when type is obvious)
   - Switch expressions (SAFE)
   - Pattern matching instanceof (SAFE)
   - Records for DTOs (CAREFUL - only pure data)
   - Text blocks (SAFE)
   - Sealed classes (CAREFUL - when hierarchy is known and fixed)

4. **Write** transformed file to `migrated_code/lifting_java17/`
   - Maintain exact package structure
   - Preserve file name
   - Keep all comments

5. **Log** transformation to report

---

## 📊 MIGRATION REPORT STRUCTURE

Create `migrated_code/docs/JAVA17_UPLIFT_REPORT.md`:

```markdown
# JAVA 17 UPLIFT REPORT

## Executive Summary
- Total files processed: X
- Total transformations applied: Y
- Build system: Gradle
- Target Java version: 17

## Transformation Statistics
### Collections Modernization
- Vector → ArrayList: X occurrences
- Hashtable → ConcurrentHashMap: Y occurrences
- Legacy collections → Modern: Z occurrences

### Resource Management
- Try-with-resources applied: X files
- AutoCloseable resources handled: Y occurrences

### API Replacements
- Date/Calendar → java.time: X occurrences
- HttpURLConnection → HttpClient: Y occurrences
- File I/O modernized: Z occurrences

### Syntax Modernization
- Lambdas introduced: X occurrences
- Records created: Y DTOs
- Switch expressions: Z occurrences
- Pattern matching: W occurrences
- Text blocks: V occurrences
- var keyword: U occurrences

### String Enhancements
- isBlank() usage: X
- lines() usage: Y
- strip() usage: Z

## Per-File Transformations
### [File path 1]
- Transformation 1: [Description]
- Transformation 2: [Description]

### [File path 2]
- Transformation 1: [Description]

## Build Configuration
- Created: build.gradle (or build.gradle.kts)
- Gradle version: 8.5
- Java toolchain: 17
- Gradle wrapper: Included

## Security Improvements
- Enhanced NullPointerException diagnostics enabled
- Modern cryptography APIs (if applicable)
- Secure random number generation (if applicable)

## Performance Improvements
- Stream API optimizations
- Compact collections where applicable
- String deduplication benefits

## Deprecated APIs Fixed
List all deprecated API replacements with line numbers

## Pending Manual Review
- [ ] Validate Records don't contain business logic
- [ ] Review sealed class hierarchies
- [ ] Verify preview features are acceptable
- [ ] Test Gradle build
- [ ] Review var usage for clarity
```

---

## 🎯 COMPLETION CHECKLIST

Before setting `agent2_completed = true`:

- [ ] All `.java` files processed from `legacy_repo/`
- [ ] All files written to `migrated_code/lifting_java17/`
- [ ] Package structure preserved
- [ ] Gradle build files created (NOT Maven)
- [ ] Gradle wrapper files included
- [ ] Deprecated APIs replaced
- [ ] Try-with-resources applied to all AutoCloseable
- [ ] Collections modernized
- [ ] Lambda conversions completed
- [ ] Report generated at `migrated_code/docs/JAVA17_UPLIFT_REPORT.md`
- [ ] `migration_state.json` updated: `"agent2_completed": true`

---

## 🚀 BUILD VALIDATION

After transformation, the following commands should work:

```bash
cd migrated_code/lifting_java17
./gradlew clean build
./gradlew test
./gradlew check
```

---

## 💡 BEST PRACTICES

1. **Preserve Intent**: Code should be more readable, not more clever
2. **Type Safety**: Don't lose type information
3. **Nullability**: Use Optional judiciously, not everywhere
4. **Immutability**: Prefer immutable collections where appropriate
5. **Performance**: Modern APIs often perform better
6. **Documentation**: Keep existing comments, add notes for major changes
7. **Testing**: Ensure behavior is preserved
8. **Gradle First**: Always generate Gradle build files, never Maven

---

## ⚠️ CAUTION AREAS

- **Records**: Only for simple DTOs without logic
- **Sealed Classes**: Only when inheritance is truly restricted
- **Pattern Matching for instanceof (JEP 394)**: FINALIZED in Java 16 - Safe to use
- **Pattern Matching for switch (JEP 406)**: PREVIEW feature in Java 17 - Requires `--enable-preview` flag
- **var**: Don't overuse - clarity over brevity
- **Text Blocks**: Ensure no unintended whitespace changes

---

## 📚 REFERENCE

Java Enhancement Proposals (JEPs) implemented:
- JEP 409: Sealed Classes (Java 17) - **FINALIZED**
- JEP 406: Pattern Matching for switch (Third Preview in Java 17) - **PREVIEW** (finalized in Java 21)
- JEP 395: Records (Java 16) - **FINALIZED**
- JEP 394: Pattern Matching for instanceof (Java 16) - **FINALIZED**
- JEP 378: Text Blocks (Java 15) - **FINALIZED**
- JEP 361: Switch Expressions (Java 14) - **FINALIZED**
- JEP 323: Local-Variable Syntax for Lambda (Java 11) - **FINALIZED**
- JEP 321: HTTP Client (Java 11) - **FINALIZED**
- JEP 286: Local-Variable Type Inference (Java 10) - **FINALIZED**
- And many more from Java 9-17

---

**END OF AGENT 2 INSTRUCTIONS**
