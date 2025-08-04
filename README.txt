/////////////////////////////////////////////////////////////////////////////
//                                                                         //
//                     stb_json - v1.7.19 (Aug 2025)                       //
//       Single-file JSON parser/generator for C/C++ in the STB style      //
//                                                                         //
/////////////////////////////////////////////////////////////////////////////

----------------------------------------------
|  _______ _____     _____ ___  _  _______   |
| |__   __/ ____|   |_   _/ _ \| |/ / ____|  |
|    | | | (___ ______| || | | | ' /|  __|   |
|    | |  \___ \______| || | | |  < | |      |
|    | |  ____) |    _| || |_| | . \| |____  |
|    |_| |_____/    |_____\___/|_|\_\______| |
|                                            |
----------------------------------------------

== ABOUT ==
A lightweight single-header JSON library for C/C++ that implements both parsing 
and generation with full RFC compliance. Designed for simplicity, portability, 
and efficiency in the tradition of Sean Barrett's stb libraries.

Created by Ferki (August 2025)

== FEATURES ==
✓ Single header file implementation
✓ No external dependencies
✓ Thread-safe error tracking
✓ Full JSON standard support (RFC 8259)
✓ Memory management hooks
✓ JSON Pointer (RFC 6901) support
✓ JSON Patch (RFC 6902) support
✓ JSON Merge Patch (RFC 7386) support
✓ File parsing utilities
✓ Efficient parsing and serialization
✓ Circular reference detection
✓ Unicode/UTF-8 validation
✓ Minification utility
✓ Fuzzer test harness

== USAGE ==
1. In ONE source file, add:
   #define STB_JSON_IMPLEMENTATION
   #include "stb_json.h"

2. In other files, just include normally:
   #include "stb_json.h"

== BASIC EXAMPLE ==


const char* json = "{\"name\":\"John\",\"age\":30,\"cars\":[\"Ford\",\"BMW\"]}";
stb_json* root = stb_json_parse(json);
if (root) {
    stb_json* name = stb_json_getobjectitem(root, "name");
    printf("Name: %s\n", name->valuestring);
    stb_json_delete(root);
}






== ADVANCED FEATURES ==


// JSON Pointer
stb_json* item = stb_json_utils_getpointer(root, "/cars/0");

// JSON Patch
stb_json* patches = stb_json_utils_generatepatches(source, target);
int result = stb_json_utils_applypatches(doc, patches);

// File parsing
stb_json* config = stb_json_parse_file("config.json");

// Minification
char json_str[] = "{ \"key\": \"value\" }";
stb_json_minify(json_str);  // Becomes {"key":"value"}

== MEMORY MANAGEMENT ==
Custom allocators can be configured:


stb_json_hooks hooks = {
    .malloc_fn = my_malloc,
    .free_fn = my_free
};
stb_json_inithooks(&hooks);




== BUILDING ==
Just include the header! For testing:
1. Enable implementation in test file
2. Compile with: 
   clang -O2 -std=c99 test.c -o test

== TESTING ==
Includes built-in fuzzer harness:
./stb_json_fuzzer testcase.json

Or use the test runner:
./stb_json_test input.json

== VERSION HISTORY ==
v1.7.19 (2025-08-04)
 - Initial public release
 - Full RFC compliance
 - Thread-safe error tracking
 - File I/O utilities

== LICENSE ==
MIT License. 
This software is free for any use.

== SUPPORT THE DEVELOPER ==
If you appreciate this software and use it in your projects, 
please consider supporting its development:

  [ https://ko-fi.com/ferki ]

Your donations help maintain and improve this library!

/////////////////////////////////////////////////////////////////////////////
//                                                                         //
//      "Simplicity is prerequisite for reliability." - Edsger Dijkstra    //
//                                                                         //
/////////////////////////////////////////////////////////////////////////////

