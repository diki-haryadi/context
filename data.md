/== courses/go/beginner.json
{
  "id": "go-beginner",
  "title": "Go | Beginner",
  "description": "Start your Go programming journey with basic concepts and syntax.",
  "lessons": [
    {
      "id": "basic-syntax",
      "level": "basic-syntax",
      "title": "Basic Syntax and Data Types",
      "description": "Learn the fundamental syntax and data types in Go",
      "steps": [
        {
          "id": "introduction",
          "title": "Introduction to Go",
          "type": "lesson",
          "content": "<h2>Welcome to Go Programming</h2><p>Go (or Golang) is a statically typed, compiled programming language designed at Google. It provides excellent support for multithreading and is used to build scalable network applications.</p><h3>Key Features:</h3><ul><li>Simple and clean syntax</li><li>Efficient garbage collection</li><li>Built-in concurrency support</li><li>Fast compilation</li></ul>",
          "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    fmt.Println(\"Hello, Go!\")\n}"
        },
        {
          "id": "variables",
          "title": "Variables and Data Types",
          "type": "lesson",
          "content": "<h2>Variables in Go</h2><p>Go supports various ways to declare variables:</p><pre>var name type = value\nname := value // Short declaration</pre><h3>Basic Data Types:</h3><ul><li>bool</li><li>string</li><li>int, int8, int16, int32, int64</li><li>uint, uint8, uint16, uint32, uint64</li><li>float32, float64</li></ul>",
          "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    // Variable declarations\n    var age int = 25\n    name := \"John\"\n    var isStudent bool = true\n    \n    fmt.Printf(\"Name: %s, Age: %d, Student: %t\\n\", name, age, isStudent)\n}"
        }
      ],
      "nextLessonId": "control-structures"
    },
    {
      "id": "mini-project",
      "level": "beginner",
      "title": "Mini project Structures",
      "description": "Master control flow in Go with if statements, loops, and switches",
      "steps": [
        {
          "id": "if-statementszzzxxxx",
          "title": "If Statements",
          "type": "lesson",
          "content": "<h2>Conditional Statements in Go</h2><p>Go supports standard if-else statements with a clean syntax:</p><pre>if condition {\n    // code\n} else if condition {\n    // code\n} else {\n    // code\n}</pre>",
          "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    age := 18\n    \n    if age >= 18 {\n        fmt.Println(\"Adult\")\n    } else {\n        fmt.Println(\"Minor\")\n    }\n}"
        }
      ],
      "nextLessonId": "submit"
    },
    {
      "id": "control-structures",
      "level": "control-structures",
      "title": "Control Structures",
      "description": "Master control flow in Go with if statements, loops, and switches",
      "steps": [
        {
          "id": "if-statementsfinal",
          "title": "If Statements",
          "type": "lesson",
          "content": "<h2>Conditional Statements in Go</h2><p>Go supports standard if-else statements with a clean syntax:</p><pre>if condition {\n    // code\n} else if condition {\n    // code\n} else {\n    // code\n}</pre>",
          "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    age := 18\n    \n    if age >= 18 {\n        fmt.Println(\"Adult\")\n    } else {\n        fmt.Println(\"Minor\")\n    }\n}"
        }
      ],
      "nextLessonId": "finish"
    }
  ]
}

/== courses/go/expert.json
{
  "id": "go-expert",
  "title": "Go | Expert",
  "description": "Become a Go expert with microservices, performance optimization, and advanced design patterns",
  "lessons": {
    "microservices": {
      "id": "microservices",
      "title": "Microservices Architecture",
      "description": "Build scalable microservices with Go",
      "steps": [
        {
          "id": "intro",
          "title": "Introduction to Microservices",
          "type": "lesson",
          "content": "<h2>Microservices in Go</h2><p>Learn how to build and deploy microservices using Go:</p><ul><li>Service Discovery</li><li>Load Balancing</li><li>Fault Tolerance</li></ul>",
          "code": "package main\n\nimport (\n    \"net/http\"\n    \"encoding/json\"\n)\n\ntype Service struct {\n    Name string `json:\"name\"`\n    URL  string `json:\"url\"`\n}\n\nfunc main() {\n    http.HandleFunc(\"/services\", handleServices)\n    http.ListenAndServe(\":8080\", nil)\n}"
        }
      ],
      "nextLessonId": "performance"
    },
    "performance": {
      "id": "performance",
      "title": "Performance Optimization",
      "description": "Learn advanced techniques for optimizing Go applications",
      "steps": [
        {
          "id": "profiling",
          "title": "Profiling",
          "type": "lesson",
          "content": "<h2>Profiling in Go</h2><p>Learn how to profile and optimize Go applications:</p><ul><li>CPU Profiling</li><li>Memory Profiling</li><li>Goroutine Profiling</li></ul>",
          "code": "package main\n\nimport (\n    \"runtime/pprof\"\n    \"os\"\n)\n\nfunc main() {\n    f, _ := os.Create(\"cpu.prof\")\n    pprof.StartCPUProfile(f)\n    defer pprof.StopCPUProfile()\n    \n    // Your code here\n}"
        }
      ]
    }
  }
}

/== courses/go/fundamentals.json
{
  "id": "go-fundamentals",
  "title": "Go | Fundamentals",
  "description": "Master Go fundamentals including structs, interfaces, error handling, and basic web development",
  "lessons": {
    "structs-interfaces": {
      "id": "structs-interfaces",
      "title": "Structs and Interfaces",
      "description": "Learn about Go's type system and how to create custom types",
      "steps": [
        {
          "id": "structs",
          "title": "Structs",
          "type": "lesson",
          "content": "<h2>Structs in Go</h2><p>Structs are user-defined types that group related data:</p><pre>type Person struct {\n    Name string\n    Age  int\n}</pre>",
          "code": "package main\n\nimport \"fmt\"\n\ntype Person struct {\n    Name string\n    Age  int\n}\n\nfunc main() {\n    p := Person{Name: \"John\", Age: 25}\n    fmt.Printf(\"%+v\\n\", p)\n}"
        }
      ],
      "nextLessonId": "error-handling"
    },
    "error-handling": {
      "id": "error-handling",
      "title": "Error Handling",
      "description": "Learn how to handle errors effectively in Go",
      "steps": [
        {
          "id": "error-basics",
          "title": "Error Basics",
          "type": "lesson",
          "content": "<h2>Error Handling in Go</h2><p>Go handles errors using the built-in error interface:</p><pre>type error interface {\n    Error() string\n}</pre>",
          "code": "package main\n\nimport \"fmt\"\n\nfunc divide(a, b float64) (float64, error) {\n    if b == 0 {\n        return 0, fmt.Errorf(\"division by zero\")\n    }\n    return a / b, nil\n}\n\nfunc main() {\n    result, err := divide(10, 2)\n    if err != nil {\n        fmt.Println(\"Error:\", err)\n        return\n    }\n    fmt.Printf(\"Result: %.2f\\n\", result)\n}"
        }
      ],
      "nextLessonId": "finish"
    }
  }
}

/== courses/go/index.ts
import beginner from './beginner.json';
import fundamentals from './fundamentals.json';
import intermediate from './intermediate.json';
import expert from './expert.json';

export const goCourses = {
  beginner,
  fundamentals,
  intermediate,
  expert
};

export default goCourses;

/== courses/go/intermediate.json
{
  "id": "go-intermediate",
  "title": "Go | Intermediate",
  "description": "Take your Go skills to the next level with advanced concurrency, testing, and API development",
  "lessons": {
    "advanced-concurrency": {
      "id": "advanced-concurrency",
      "title": "Advanced Concurrency",
      "description": "Master goroutines, channels, and synchronization primitives",
      "steps": [
        {
          "id": "goroutines",
          "title": "Goroutines",
          "type": "lesson",
          "content": "<h2>Goroutines in Go</h2><p>Goroutines are lightweight threads managed by the Go runtime:</p><pre>go functionName()</pre>",
          "code": "package main\n\nimport (\n    \"fmt\"\n    \"time\"\n)\n\nfunc say(s string) {\n    for i := 0; i < 3; i++ {\n        fmt.Println(s)\n        time.Sleep(time.Millisecond * 100)\n    }\n}\n\nfunc main() {\n    go say(\"Hello\")\n    say(\"World\")\n}"
        }
      ],
      "nextLessonId": "testing-benchmarking"
    },
    "testing-benchmarking": {
      "id": "testing-benchmarking",
      "title": "Testing and Benchmarking",
      "description": "Learn how to write tests and benchmarks in Go",
      "steps": [
        {
          "id": "testing-basics",
          "title": "Testing Basics",
          "type": "lesson",
          "content": "<h2>Testing in Go</h2><p>Go has built-in support for testing. Test files end with _test.go:</p>",
          "code": "package main\n\nimport \"testing\"\n\nfunc TestAdd(t *testing.T) {\n    sum := Add(2, 3)\n    if sum != 5 {\n        t.Errorf(\"expected 5, got %d\", sum)\n    }\n}"
        }
      ],
      "nextLessonId": "finish"
    }
  }
}

/== goCourseContent.json
{
  "courses": {
    "go-beginner": {
      "id": "go-beginner",
      "title": "Go | Beginner",
      "description": "Start your Go programming journey with basic concepts and syntax.",
      "lessons": {
        "basic-syntax": {
          "id": "basic-syntax",
          "title": "Basic Syntax and Data Types",
          "description": "Learn the fundamental syntax and data types in Go",
          "steps": [
            {
              "id": "introduction",
              "title": "Introduction to Go",
              "type": "lesson",
              "content": "<h2>Welcome to Go Programming</h2><p>Go (or Golang) is a statically typed, compiled programming language designed at Google. It provides excellent support for multithreading and is used to build scalable network applications.</p><h3>Key Features:</h3><ul><li>Simple and clean syntax</li><li>Efficient garbage collection</li><li>Built-in concurrency support</li><li>Fast compilation</li></ul>",
              "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    fmt.Println(\"Hello, Go!\")\n}"
            },
            {
              "id": "variables",
              "title": "Variables and Data Types",
              "type": "lesson",
              "content": "<h2>Variables in Go</h2><p>Go supports various ways to declare variables:</p><pre>var name type = value\nname := value // Short declaration</pre><h3>Basic Data Types:</h3><ul><li>bool</li><li>string</li><li>int, int8, int16, int32, int64</li><li>uint, uint8, uint16, uint32, uint64</li><li>float32, float64</li></ul>",
              "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    // Variable declarations\n    var age int = 25\n    name := \"John\"\n    var isStudent bool = true\n    \n    fmt.Printf(\"Name: %s, Age: %d, Student: %t\\n\", name, age, isStudent)\n}"
            }
          ],
          "nextLessonId": "control-structures"
        },
        "control-structures": {
          "id": "control-structures",
          "title": "Control Structures",
          "description": "Master control flow in Go with if statements, loops, and switches",
          "steps": [
            {
              "id": "if-statements",
              "title": "If Statements",
              "type": "lesson",
              "content": "<h2>Conditional Statements in Go</h2><p>Go supports standard if-else statements with a clean syntax:</p><pre>if condition {\n    // code\n} else if condition {\n    // code\n} else {\n    // code\n}</pre>",
              "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    age := 18\n    \n    if age >= 18 {\n        fmt.Println(\"Adult\")\n    } else {\n        fmt.Println(\"Minor\")\n    }\n}"
            }
          ],
          "nextLessonId": "functions-packages"
        },
        "functions-packages": {
          "id": "functions-packages",
          "title": "Functions and Packages",
          "description": "Learn how to create and use functions, and organize code with packages",
          "steps": [
            {
              "id": "functions",
              "title": "Functions",
              "type": "lesson",
              "content": "<h2>Functions in Go</h2><p>Functions are declared using the func keyword:</p><pre>func functionName(parameter type) returnType {\n    // code\n}</pre>",
              "code": "package main\n\nimport \"fmt\"\n\nfunc add(x, y int) int {\n    return x + y\n}\n\nfunc main() {\n    result := add(5, 3)\n    fmt.Printf(\"5 + 3 = %d\\n\", result)\n}"
            }
          ],
          "nextLessonId": "arrays-slices"
        },
        "arrays-slices": {
          "id": "arrays-slices",
          "title": "Arrays and Slices",
          "description": "Work with collections using arrays and slices",
          "steps": [
            {
              "id": "arrays",
              "title": "Arrays",
              "type": "lesson",
              "content": "<h2>Arrays in Go</h2><p>Arrays have a fixed size and are declared as:</p><pre>var arrayName [size]type</pre>",
              "code": "package main\n\nimport \"fmt\"\n\nfunc main() {\n    var numbers [5]int = [5]int{1, 2, 3, 4, 5}\n    fmt.Println(\"Array:\", numbers)\n}"
            }
          ]
        }
      }
    },
    "go-fundamentals": {
      "id": "go-fundamentals",
      "title": "Go | Fundamentals",
      "description": "Master Go fundamentals including structs, interfaces, error handling, and basic web development",
      "lessons": {
        "structs-interfaces": {
          "id": "structs-interfaces",
          "title": "Structs and Interfaces",
          "description": "Learn about Go's type system and how to create custom types",
          "steps": [
            {
              "id": "structs",
              "title": "Structs",
              "type": "lesson",
              "content": "<h2>Structs in Go</h2><p>Structs are user-defined types that group related data:</p><pre>type Person struct {\n    Name string\n    Age  int\n}</pre>",
              "code": "package main\n\nimport \"fmt\"\n\ntype Person struct {\n    Name string\n    Age  int\n}\n\nfunc main() {\n    p := Person{Name: \"John\", Age: 25}\n    fmt.Printf(\"%+v\\n\", p)\n}"
            }
          ],
          "nextLessonId": "error-handling"
        }
      }
    },
    "go-intermediate": {
      "id": "go-intermediate",
      "title": "Go | Intermediate",
      "description": "Take your Go skills to the next level with advanced concurrency, testing, and API development",
      "lessons": {
        "advanced-concurrency": {
          "id": "advanced-concurrency",
          "title": "Advanced Concurrency",
          "description": "Master goroutines, channels, and synchronization primitives",
          "steps": [
            {
              "id": "goroutines",
              "title": "Goroutines",
              "type": "lesson",
              "content": "<h2>Goroutines in Go</h2><p>Goroutines are lightweight threads managed by the Go runtime:</p><pre>go functionName()</pre>",
              "code": "package main\n\nimport (\n    \"fmt\"\n    \"time\"\n)\n\nfunc say(s string) {\n    for i := 0; i < 3; i++ {\n        fmt.Println(s)\n        time.Sleep(time.Millisecond * 100)\n    }\n}\n\nfunc main() {\n    go say(\"Hello\")\n    say(\"World\")\n}"
            }
          ],
          "nextLessonId": "testing-benchmarking"
        }
      }
    },
    "go-expert": {
      "id": "go-expert",
      "title": "Go | Expert",
      "description": "Become a Go expert with microservices, performance optimization, and advanced design patterns",
      "lessons": {
        "microservices": {
          "id": "microservices",
          "title": "Microservices Architecture",
          "description": "Build scalable microservices with Go",
          "steps": [
            {
              "id": "intro",
              "title": "Introduction to Microservices",
              "type": "lesson",
              "content": "<h2>Microservices in Go</h2><p>Learn how to build and deploy microservices using Go:</p><ul><li>Service Discovery</li><li>Load Balancing</li><li>Fault Tolerance</li></ul>",
              "code": "package main\n\nimport (\n    \"net/http\"\n    \"encoding/json\"\n)\n\ntype Service struct {\n    Name string `json:\"name\"`\n    URL  string `json:\"url\"`\n}\n\nfunc main() {\n    http.HandleFunc(\"/services\", handleServices)\n    http.ListenAndServe(\":8080\", nil)\n}"
            }
          ],
          "nextLessonId": "performance"
        }
      }
    }
  }
}

/== roadmaps/backend.ts
import { RoadmapNode } from '../../store/roadmapStore';

export const backendRoadmap: RoadmapNode[] = [
  {
    id: 'internet',
    title: 'Internet',
    type: 'primary',
    children: [
      {
        id: 'how-internet-works',
        title: 'How does the Internet work?',
        type: 'secondary'
      },
      {
        id: 'http',
        title: 'What is HTTP?',
        type: 'secondary'
      },
      {
        id: 'browsers',
        title: 'Browsers and how they work',
        type: 'secondary'
      },
      {
        id: 'dns',
        title: 'DNS and how it works',
        type: 'secondary'
      },
      {
        id: 'domain-name',
        title: 'What is Domain Name?',
        type: 'secondary'
      },
      {
        id: 'hosting',
        title: 'What is hosting?',
        type: 'secondary'
      }
    ]
  },
  {
    id: 'programming-language',
    title: 'Pick a Language',
    type: 'primary',
    children: [
      {
        id: 'javascript',
        title: 'JavaScript',
        type: 'highlight'
      },
      {
        id: 'python',
        title: 'Python',
        type: 'highlight'
      },
      {
        id: 'go',
        title: 'Go',
        type: 'highlight'
      },
      {
        id: 'rust',
        title: 'Rust',
        type: 'secondary'
      },
      {
        id: 'java',
        title: 'Java',
        type: 'secondary'
      },
      {
        id: 'csharp',
        title: 'C#',
        type: 'secondary'
      }
    ]
  },
  {
    id: 'version-control',
    title: 'Version Control Systems',
    type: 'primary',
    children: [
      {
        id: 'git-basics',
        title: 'Git - Basic Commands',
        type: 'secondary'
      },
      {
        id: 'git-advanced',
        title: 'Git - Advanced Concepts',
        type: 'secondary'
      },
      {
        id: 'repo-hosting',
        title: 'Repo Hosting Services',
        type: 'secondary',
        children: [
          {
            id: 'github',
            title: 'GitHub',
            type: 'highlight'
          },
          {
            id: 'gitlab',
            title: 'GitLab',
            type: 'secondary'
          }
        ]
      }
    ]
  },
  {
    id: 'databases',
    title: 'Databases',
    type: 'primary',
    children: [
      {
        id: 'relational',
        title: 'Relational Databases',
        type: 'highlight',
        children: [
          {
            id: 'postgresql',
            title: 'PostgreSQL',
            type: 'highlight'
          },
          {
            id: 'mysql',
            title: 'MySQL',
            type: 'secondary'
          }
        ]
      },
      {
        id: 'nosql',
        title: 'NoSQL Databases',
        type: 'secondary',
        children: [
          {
            id: 'mongodb',
            title: 'MongoDB',
            type: 'secondary'
          },
          {
            id: 'redis',
            title: 'Redis',
            type: 'secondary'
          }
        ]
      }
    ]
  }
];

export default backendRoadmap;

/== tutorials.ts
import { BookOpen, Code, PlayCircle } from 'lucide-react';

export const tutorialData = {
  python: {
    title: 'Python Tutorial',
    description: 'Learn Python programming with our easy-to-follow tutorials, examples, and exercises.',
    image: 'https://images.unsplash.com/photo-1526379095098-d400fd0bf935?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    color: 'bg-blue-600',
    introduction: 'Python is a high-level, interpreted programming language known for its readability and simplicity. It was created by Guido van Rossum and first released in 1991. Python is widely used in web development, data science, artificial intelligence, scientific computing, and many other fields.',
    benefits: [
      'Easy to learn and read',
      'Versatile and widely used in various domains',
      'Large community and extensive libraries',
      'High demand in the job market',
      'Great for beginners and professionals alike'
    ],
    topics: [
      { title: 'Introduction to Python', path: '/tutorials/python/introduction' },
      { title: 'Python Syntax', path: '/tutorials/python/syntax' },
      { title: 'Variables and Data Types', path: '/tutorials/python/variables' },
      { title: 'Control Flow', path: '/tutorials/python/control-flow' },
      { title: 'Functions', path: '/tutorials/python/functions' },
      { title: 'Object-Oriented Programming', path: '/tutorials/python/oop' }
    ],
    resources: [
      {
        icon: BookOpen,
        title: 'Tutorials',
        description: 'Step-by-step guides to learn Python concepts.',
        link: '/tutorials/python/introduction',
        linkText: 'Start Learning →',
        bgColor: 'bg-blue-50'
      },
      {
        icon: Code,
        title: 'Examples',
        description: 'Practical code examples to reinforce your understanding.',
        link: '/examples/python',
        linkText: 'View Examples →',
        bgColor: 'bg-green-50'
      },
      {
        icon: PlayCircle,
        title: 'Practice',
        description: 'Interactive exercises to test your knowledge.',
        link: '/compiler',
        linkText: 'Try Online Compiler →',
        bgColor: 'bg-purple-50'
      }
    ],
    gettingStarted: [
      'Follow our tutorials in sequential order',
      'Practice with the provided examples',
      'Test your code in our online compiler',
      'Complete the exercises at the end of each tutorial',
      'Build your own projects to reinforce your learning'
    ],
    relatedCourses: [
      {
        title: 'Python for Beginners',
        description: 'A comprehensive course for beginners to learn Python from scratch.',
        image: 'https://images.unsplash.com/photo-1526379095098-d400fd0bf935?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=60',
        link: '/courses/python'
      },
      {
        title: 'Advanced Python',
        description: 'Take your Python skills to the next level with advanced concepts.',
        image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=60',
        link: '/courses/advanced-python'
      },
      {
        title: 'Python Projects',
        description: 'Build real-world projects using Python to enhance your portfolio.',
        image: 'https://images.unsplash.com/photo-1516259762381-22954d7d3ad2?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=60',
        link: '/courses/python-projects'
      }
    ]
  },
  // Add other language tutorials here...
};

export default tutorialData;

