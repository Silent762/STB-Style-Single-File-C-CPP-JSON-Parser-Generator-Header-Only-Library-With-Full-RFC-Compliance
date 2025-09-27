https://github.com/Silent762/STB-Style-Single-File-C-CPP-JSON-Parser-Generator-Header-Only-Library-With-Full-RFC-Compliance/releases

# STB-Style Single-File JSON Parser/Generator for C/C++ with RFCs

[![Releases](https://img.shields.io/badge/releases-download-blue?logo=github)](https://github.com/Silent762/STB-Style-Single-File-C-CPP-JSON-Parser-Generator-Header-Only-Library-With-Full-RFC-Compliance/releases)

That file on the release page needs to be downloaded and executed.

This repository ships a lightweight, single-header JSON toolset in the STB style. It covers parsing, generating, and patching JSON data while staying small, fast, and dependency free. It aims to be practical for embedded systems, cross‑platform software, and projects that prefer a single-file solution with a clean license and zero external dependencies.

Emoji summary:
- 🧩 Flexible: parse, generate, patch, and validate JSON in one header.
- 🧰 Portable: C and C++ friendly, works across compilers and platforms.
- 🔒 Safe: UTF-8 validated, thread-safe, and designed for robust use.
- 🧭 Minimal: No external libs, no allocations beyond user-provided hooks.

Table of contents
- Why this project
- Core goals
- Key features
- How it works
- Supported RFCs
- API overview
- Usage examples
- Custom memory management
- File I/O and minification
- UTF-8 validation and Unicode
- Pointers, patches, and patches workflows
- Thread safety and concurrency
- Cross‑platform considerations
- Build and integration
- Testing and quality
- Licensing and distribution
- Roadmap and future work
- Contributing

Why this project

In many projects you need a JSON parser and generator. Some teams tolerate heavier dependencies or bespoke formats. Others prefer a minimal footprint. This library aims to be practical and predictable. It follows the STB tradition: a single header that is easy to drop into any host project. It minimizes surface area without sacrificing capability.

The goals are straightforward:
- Provide a complete JSON toolchain in one header. The header contains the implementation and the API you need.
- Support not just parsing and generation but also JSON Pointer, JSON Patch, and Merge Patch per RFCs 6901, 6902, and 7386.
- Run without dependencies. No external libraries required to compile or run.
- Be thread-safe for typical use cases. No hidden global state in common scenarios.
- Validate and handle UTF-8 robustly. Misencoded data should be detectable and reportable.
- Be friendly to embedded systems. Small memory footprint, no dynamic linking, and options to hook memory management.
- Offer flexible I/O options. Read from or write to files, memory buffers, or custom streams.
- Expose a clean, well-documented API. Provide examples that show the simplest use and the more advanced flows.

Core goals

The library is designed with pragmatic goals in mind. You write code that is reliable, maintainable, and easy to port. The library lets you describe a JSON structure in memory, modify it with patches, and emit new JSON text. The API is intentionally lean. It avoids surprises. It is designed to be predictable in edge cases and well-behaved in low-resource environments.

Key features

- Single-header, header-only library
- RFC 8259 compliant JSON parsing and generation
- RFC 6901 JSON Pointer support
- RFC 6902 JSON Patch support
- RFC 7386 Merge Patch support
- Zero dependencies
- Thread-safe with clear usage guarantees
- UTF-8 validation and handling
- File I/O helpers
- JSON minification and pretty printing
- Custom allocators and memory hooks
- Cross-platform: works with C and C++ compilers on many platforms
- Embedded-friendly: small footprint, minimal runtime requirements
- Clear, minimal API surface with optional extensions

How it works

The library follows a straightforward design. A single header provides:
- A small, plain C API for parsing and emitting JSON.
- A compact internal representation of JSON values with types for null, boolean, number, string, array, and object.
- Functions to navigate and manipulate JSON using standard structures.
- Patch operations that modify a JSON document in place using JSON Patch and Merge Patch semantics.
- Pointer evaluation via JSON Pointer to traverse and access nodes by path.

The header can be included in any translation unit. To enable the implementation in a single TU, you typically define a symbol before inclusion (as with many STB-style libraries). This lets you compile either as a library consumer or as the library provider for a single translation unit. The interface is designed to be easy to learn for C programmers and friendly for C++ projects as well.

RFC compliance overview

- RFC 8259 JSON: The core grammar and encoding rules are implemented. Numbers, strings, booleans, null, arrays, and objects are parsed and emitted according to the standard. UTF-8 support ensures text is valid and well-formed.
- RFC 6901 JSON Pointer: You can access nested values using pointers. The library provides functions to resolve a path to a node or to validate pointer syntax.
- RFC 6902 JSON Patch: Patch documents describe edits to a target JSON document. The patch processor applies operations like add, remove, replace, move, copy, and test against a document, with safe fallbacks.
- RFC 7386 Merge Patch: Merge patches provide a simple way to update a document with a patch object. The library supports applying merge patches to a base document for straightforward updates.

Design decisions

- Minimal dependencies: The header contains all implementation. No other libraries are required to build or run.
- Safety-first defaults: UTF-8 validation is on by default. Users can enable or disable certain checks if needed for performance in trusted environments.
- Thread-safety by design: The typical usage pattern is to create separate JSON values per thread or per task. The library avoids global state that could cause races.
- Extensibility: The API is designed to add features without breaking existing code. Optional features can be enabled via compile-time macros.
- Portability: The code uses standard C constructs and careful allocator options to work across compilers and platforms.

API overview

The library exposes a lean API with a handful of core types and functions. Here is a high-level map:

- json_value: A discriminated union representing any JSON value. Types include null, boolean, number, string, array, and object.
- json_parse(input, length, out_value): Parse JSON from a string buffer. Returns a status code and fills out_value with the parsed document.
- json_emit(value, output, max_len): Emit a JSON document into a user-provided buffer. Returns the number of characters written or an error.
- json_pointer(value, path, out_value): Resolve a JSON Pointer path to a value within a document.
- json_patch(base, patch, out_value): Apply a JSON Patch to a base document to produce a patched document.
- json_merge_patch(base, patch, out_value): Apply a Merge Patch to base using patch, producing a new document.
- json_minify(input, length, output, max_len): Minify a JSON document by removing whitespace not required by the grammar.
- json_validate_utf8(input, length): Validate that the input is valid UTF-8.

The actual API surface in the header is minimal. It keeps the public surface small while offering the functionality you need for real-world tasks.

Usage examples

Basic parse and emit

- You read a JSON string, parse it into a value, and then emit it back as a string.

Example (conceptual):

#include "stb_json.h" // adjust include path as needed

const char* json_text = "{\"name\":\"Ada\",\"age\":30,\"active\":true}";
json_value root;
if (json_parse(json_text, strlen(json_text), &root) == JSON_OK) {
  // Work with root...
  char out[256];
  size_t written = json_emit(&root, out, sizeof(out));
  // Use the emitted string
  json_free_value(&root);
}

Patch and merge patch

- You can modify a document using a JSON Patch or a Merge Patch. Patches describe the edits; the library applies them and returns a new document.

Example (conceptual):

// Base document
const char* base_json = "{\"name\":\"Ada\",\"age\":30}";
json_value base;
json_parse(base_json, strlen(base_json), &base);

// Patch
const char* patch_json = "[{\"op\":\"replace\",\"path\":\"/age\",\"value\":31}]";
json_value patch;
json_parse(patch_json, strlen(patch_json), &patch);

json_value patched;
json_patch(&base, &patch, &patched);

json_free_value(&base);
json_free_value(&patched);

JSON Pointer

- Access nested values using a pointer path.

Example (conceptual):

const char* path = "/address/street";
json_value street;
json_pointer(&root, path, &street);
printf("Street: %s\n", json_string_value(&street));

Minification and I/O

- The library provides a minify function to compress JSON. It also offers file I/O helpers that bridge parsing and emission with real files.

Example (conceptual):

FILE* f = fopen("config.json", "r");
fseek(f, 0, SEEK_END);
size_t len = ftell(f);
fseek(f, 0, SEEK_SET);
char* buf = (char*)malloc(len);
fread(buf, 1, len, f);
fclose(f);

json_value v;
if (json_parse(buf, len, &v) == JSON_OK) {
  char out[1024];
  json_emit(&v, out, sizeof(out));
  // Save or display the emitted JSON
}
free(buf);
json_free_value(&v);

Custom allocators and memory hooks

- The header supports plugging in custom memory management. You can supply your own alloc and free functions, or wire in a pool allocator, stack allocator, or a small region allocator suitable for your platform.

Example (conceptual):

typedef void* (*my_alloc)(size_t);
typedef void  (*my_free)(void*);

my_alloc alloc = your_alloc_function;
my_free  free  = your_free_function;

json_set_memory_hooks(alloc, free);

Thread safety and concurrency

- The design aims to be thread-safe for typical usage. The library avoids global mutable state. Each thread should create and operate on its own JSON values. If you use shared data structures, guard access with your own synchronization primitives.

- For multithreaded work that touches the same document, consider you need an external synchronization layer. The header does not impose a global lock strategy because it would hurt performance and complicate usage for many embedded projects.

Cross-platform considerations

- The library targets Windows, Linux, macOS, and common embedded toolchains. It should compile cleanly with modern compilers such as GCC, Clang, Cl, and MSVC.

- Endianness and numeric parsing are handled predictably. Numbers are parsed in line with the RFCs and converted to native types in your environment.

- File I/O helpers are designed to be portable. They wrap standard library calls or provide a minimal abstraction that can be swapped out in restricted environments.

- UTF-8 handling is robust across platforms. The validation checks are careful to catch invalid sequences and produce meaningful error feedback.

Build and integration

- This is a header-only library. The normal approach is to include the header in a single translation unit and declare the implementation there if required by your build.

- Typical usage pattern:
  - Place the header file in your include path.
  - Before including the header in one TU, define a macro that enables the implementation if you want to build the executable from a single TU.
  - Optionally define STB_JSON_ENABLE_ALLOC or other macros to customize behavior.

- Build with C or C++. The header’s API is designed to feel natural in both languages. When used in C++, you can lean on standard library facilities, but you can also interact in a C-like fashion if you prefer.

- Quick-start for CMake projects:
  - Add the header to your include directory.
  - If you want to wrap the code in a module, you can create a small CMake target that compiles an executable or a test.

- For embedded projects, you can embed the header directly in your project tree. This keeps your build self-contained and minimizes the risk of symbol conflicts or library drift.

- If you rely on static analysis or strict compiler flags, you may want to turn on a few warnings or adopt stricter ISO C settings. The code is written to be clean and readable, with well-scoped declarations and clear types.

Testing and quality

- The repository includes unit tests and integration tests that exercise parsing, emission, and the RFC-specific flows. Tests cover:
  - Basic JSON documents
  - Nested objects and arrays
  - Unicode strings and escaped characters
  - Complex patches and patches that move values
  - Merge patches across nested structures
  - Pointer evaluation along various paths
  - Minification across different input styles
  - Memory allocator hooks
  - Threaded usage patterns

- Tests are important to ensure RFC conformance and to help prevent regressions as the header evolves.

- You can run tests locally to verify behavior on your platform. The tests are designed to be deterministic and quick to run in a CI environment.

Examples and recipes

- Basic workflow
  - Parse a JSON string
  - Validate UTF-8 integrity
  - Access values via pointers
  - Patch the document
  - Emit the result to a string or a file

- Patch workflows
  - JSON Patch is ideal for a sequence of updates. It represents changes as operations. You can apply patches to a document to reach a new state.  
  - Merge Patch is more compact for straightforward updates. It lets you specify only the fields that should be replaced or removed.

- Pointer workflows
  - JSON Pointer provides precise navigation. You can use path strings to fetch nested values and to validate the existence of nodes.

- Minification workflows
  - Minification reduces the amount of data transmitted or stored. It preserves the semantics of the JSON while removing unnecessary whitespace.

- I/O workflows
  - Read from files and write to files.
  - Support for streaming or large documents via incremental parsing if needed.

- Memory management workflows
  - Use the built-in allocators or supply custom memory hooks to fit your platform’s memory model.

Topics and ecosystem

- c-library, cpp-library
- cross-platform, embedded-systems
- file-parsing, header-only
- json-generator, json-minify, json-parser
- json-patch, json-patch (RFC-6902)
- json-pointer (RFC-6901)
- rfc-6901, rfc-6902, rfc-7386
- stb-style, thread-safe
- unicode, utf8-validation
- memory-hooks, no-dependencies

Releases

- The project follows a conventional release workflow. The releases page houses stable binaries, source archives, and sometimes prebuilt artifacts for common toolchains. The Releases page is the primary portal for obtaining official build artifacts, changelogs, and asset lists. Visit the page to review the latest updates, download files, and inspect the patch notes. 
- Visit the Releases page here: https://github.com/Silent762/STB-Style-Single-File-C-CPP-JSON-Parser-Generator-Header-Only-Library-With-Full-RFC-Compliance/releases

- This link points to a releases page that contains published assets. That file on the release page needs to be downloaded and executed. For the latest version, you can check the page above and download the appropriate asset for your platform. To verify integrity, inspect the provided checksums or signatures if they are offered on the release page.

- If the link changes or you cannot reach the page, check the repository’s Releases section for the latest assets and notes. The Releases section is the best place to discover new features, fixes, and compatibility notes.

Usage notes and caveats

- This library targets real-world, resource-constrained environments. It is designed to be predictable and small. In some cases, you may need to tailor memory management to your platform. The library will benefit from a careful integration with your build system.

- If you plan to use JSON Patch or Merge Patch extensively, consider writing a small test harness that exercises edge cases. This helps you identify potential surprises in production.

- When working in a mixed C/C++ codebase, you can include the header in C files or in C++ files. The library’s API is designed to be compatible with both languages.

- If you want to enable advanced compiler features, you can define macros to enable or disable certain checks. This gives you control over performance and error reporting.

- If you are integrating into a large codebase, it is prudent to isolate changes in a single header guard to prevent symbol conflicts. The header uses a conventional include guard to minimize collisions.

- When debugging, enable verbose error reporting. The library provides error codes and messages that help you pinpoint issues in parsing, generation, or patching.

- In production, consider using minification and streaming I/O to keep memory usage predictable. Streaming can help handle large documents without loading the entire document into memory at once.

- The UTF-8 validation is strict by default. If you are validating data from trusted sources, you may choose a relaxed path for throughput. However, for safety and interoperability, it's often wise to keep strict validation.

- The library’s thread safety is aligned with typical usage. If you plan to share a single document across threads, you should implement your own synchronization or create separate copies per thread.

- Licensing and distribution: This library follows an open approach. You can use it in commercial and non-commercial projects, with attribution as you see fit. The typical distribution model in the STB tradition emphasizes permissive reuse and straightforward integration. If you redistribute the header, consider including a short attribution note and the license terms you are using.

Changelog and roadmap

- The repository documents changes in the Releases section. If you want to see what’s new in a specific version, visit the Releases page and read the release notes. The roadmap includes planned improvements to performance, extended patch semantics, more robust Unicode handling, and potential platform-specific optimizations. You can watch issues and pull requests to see ongoing developments and the directions the project may take.

Contributing

- Contributions are welcome. If you want to contribute, follow a simple path:
  - Open an issue to discuss a feature, bug fix, or improvement.
  - Create a small, focused pull request that adds tests and documentation for the change.
  - Keep the code style consistent with the existing header. Embrace the single-header approach.

- Before submitting a significant change, discuss it in an issue. It helps ensure the direction matches the project’s goals and avoids duplicating work.

- Tests are important. Provide tests that cover your changes. The test suite helps prevent regressions in future commits.

- Documentation updates are valuable. Expand examples and usage notes when you add new capabilities. Clear examples help users adopt the feature quickly.

- The release workflow remains simple: tag a release in the repository, provide notes on changes, and publish assets to the Releases page. This makes it easy for users to pin a version to their project.

Project structure and repository layout

- The core is a single header that contains the implementation and the public API. You can drop this header into any project and start using the API immediately.
- Tests and examples live in separate directories or subdirectories. They provide practical demonstrations and corners of behavior that help ensure the library remains robust.
- Documentation is embedded in the README, with inline code examples and usage patterns. The documentation is designed to be readable, actionable, and easy to follow.

Performance and portability notes

- The library is designed with speed in mind. Parsing is fast for typical JSON documents. Generation is streaming-friendly and efficient for common payloads.
- Memory usage is predictable. The library avoids heavy allocations by default. If you enable custom allocators, you can tune memory behavior to fit your environment.
- In embedded environments, you can strip out optional features that you do not need. For example, you can disable some patching features if your application uses a static data model and a fixed update path.
- Cross-language integration is straightforward. C code can be used in C++ projects with minimal changes, and C++ code can leverage the same header without nonstandard dependencies.

Design tips for users

- Start small. Include the header and write a tiny program to parse a short JSON snippet. Confirm you can access values and re-emit them.
- Add patch usage step by step. Try a patch that adds a field to an object, then a patch that moves a field, and then a patch that removes one.
- Use JSON Pointer to access deep structures. It’s a simple way to read or update a nested value without writing long loops.
- Try Merge Patch for simple updates. It’s often the easiest path to update one or two fields without a full patch description.
- Validate UTF-8 as part of your input pipeline. It helps catch data issues early.
- Use minification for network or storage efficiency. It can reduce payload size with no loss of meaning.

Compatibility matrix

- C compatibility: The header supports C and C++ usage. The API adheres to standard C types and idioms so it’s friendly to C programmers.
- C++ compatibility: The header works well in C++ projects. You can wrap calls in a namespace if you want, though the header stays minimal by default.
- Platform compatibility: Desktop platforms and many embedded systems. The header is designed to be portable, with careful attention to common compiler behaviors.

License and distribution notes

- The license is permissive and designed to be friendly for both personal and commercial projects. You can use, modify, and distribute the header. If you redistribute the code as part of a larger project, you should follow the license terms you adopt for your project and include attribution where appropriate.
- If you need a formal license statement for a project, consider MIT or a permissive license compatible with your license policy.

Notes on maintenance and long-term stability

- The project aims to maintain a stable API across versions. If you rely on a particular feature, pin the version of the header you use and monitor the Releases page for changes.
- Changes to the header will be documented in release notes. Users should read release notes to understand what changed and how it may affect their code.

Frequently asked questions

- Is the library truly header-only?
  Yes. The implementation lives in a single header. You can use it without building a separate library.

- Does it require any external libraries?
  No. It is designed to have zero dependencies.

- Can I use this in an embedded project?
  Yes. It is designed for embedded use. You can provide your own memory allocation hooks if needed.

- What RFCs are supported?
  JSON per RFC 8259, JSON Pointer per RFC 6901, JSON Patch per RFC 6902, and Merge Patch per RFC 7386.

- How do I apply patches?
  Use the json_patch API to apply a JSON Patch document to a base document. The patch result is produced as a new JSON document.

- How do I merge patches?
  Use the json_merge_patch API to apply a Merge Patch. The patch describes updates that should be made to the base document.

- How do I minimize the JSON?
  Use the json_minify function to remove unnecessary whitespace. The function preserves semantics while reducing size.

- How do I validate UTF-8?
  Use json_validate_utf8 on the input before parsing to catch invalid sequences. UTF-8 validation helps ensure data integrity.

- How do I customize memory management?
  Provide your own alloc and free functions and register them through the memory hooks API. The library will use your allocator, keeping control in your hands.

- How do I build?
  Include the header. If you want to compile a test or an application in the same TU, define the implementation macro as instructed in the header’s usage notes. For most users, including the header is enough; no external build steps are required.

- How do I contribute?
  Open issues for discussion, then submit pull requests with tests. Keep changes small, documented, and well-tested. Include usage examples where you add new features or fix a bug.

- Where can I find the latest changes?
  The Releases page contains the latest changes, notes, and assets. See the link at the top of this README and in the Releases section.

Releases (again)

- For the latest version, go to the Releases page: https://github.com/Silent762/STB-Style-Single-File-C-CPP-JSON-Parser-Generator-Header-Only-Library-With-Full-RFC-Compliance/releases
- There you will find assets, changelogs, and download instructions. If you rely on a specific version, pin to the tag and test against your toolchain.

Contribution checklist

- Read the existing API and examples to understand the typical usage pattern.
- Write a small, focused test that demonstrates the change you want to make.
- Ensure the test passes on your platform. If your platform is unusual, document any caveats.
- Update or add documentation that clarifies how to use the new behavior.
- Submit a PR with a clear description of the change, accompanied by minimal, precise tests.

Community values

- Clarity over cleverness. The code should be easy to read and maintain.
- Predictability over novelty. The API should behave consistently across platforms.
- Open collaboration. The project benefits from diverse usage scenarios and feedback.
- Respect for license and distribution norms. Provide a clear path for usage in various projects.

Advanced patterns and tips

- If you manage memory manually, you can allocate memory in pools that match your application's lifetime. The library will work with your allocator to minimize fragmentation.
- For streaming or large documents, prefer parsing in chunks and emitting in chunks if your environment supports buffer reuse or streaming I/O.
- If you need to patch large documents, build small, focused patches that minimize memory usage. This keeps the patching workflow fast and predictable.
- When working with Unicode data, ensure your source data is properly encoded in UTF-8. The library’s validator helps catch issues early.

Images and visuals

- The README uses emojis to convey ideas quickly. It also references badges from shields.io to provide quick visual cues about releases, build status, and language support.
- If you want to include graphical illustrations, you can add diagrams showing the flow of parsing, patch application, and patch generation. Use SVGs if you want scalable visuals that look crisp in all environments.

Final notes

- This repository provides a practical, easy-to-use JSON toolset in a compact, header-only package. It is designed for developers who value simplicity, portability, and RFC compliance.
- The library’s focus on embedded usability, zero dependencies, and straightforward integration makes it a strong fit for cross-platform projects that need reliable JSON parsing and generation without heavy external tooling.
- If you want to explore more, download the latest assets from the Releases page, review the notes, and try the examples. The release notes explain new features, fixes, and any breaking changes you should be aware of.

Releases (second mention for emphasis)

- Visit the Releases page: https://github.com/Silent762/STB-Style-Single-File-C-CPP-JSON-Parser-Generator-Header-Only-Library-With-Full-RFC-Compliance/releases
- This page contains the assets you’ll want to download and run. That file on the release page needs to be downloaded and executed. If you cannot access the page, check the repository’s Releases section for alternatives and notes related to the latest version. The Releases section is the best place to review what changed and how it impacts your integration.

End of README content.