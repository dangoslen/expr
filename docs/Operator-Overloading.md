# Operator Overloading

**Expr** supports operator overloading. For example, you may rewrite a such 
expression:

```js
Now().Sub(CreatedAt) 
```

To use `-` operator:
 
```js
Now() - CreatedAt
```

To overloading the operator use 
[expr.Operator](https://pkg.go.dev/github.com/antonmedv/expr?tab=doc#Operator):

```go
package main

import (
	"fmt"
	"time"

	"github.com/antonmedv/expr"
)

func main() {
	code := `Now() - CreatedAt`

	// We can define options before compiling.
	options := []expr.Option{
		expr.Env(Env{}),
		expr.Operator("-", "Sub"), // Replace `-` operator with function `Sub`.
	}

	program, err := expr.Compile(code, options...)
	if err != nil {
		panic(err)
	}

	env := Env{
		CreatedAt: time.Date(1987, time.November, 24, 20, 0, 0, 0, time.UTC),
	}

	output, err := expr.Run(program, env)
	if err != nil {
		panic(err)
	}
	fmt.Print(output)
}

type Env struct {
	datetime
	CreatedAt time.Time
}

// Functions may be defined on embedded structs as well.
type datetime struct{}

func (datetime) Now() time.Time                   { return time.Now() }
func (datetime) Sub(a, b time.Time) time.Duration { return a.Sub(b) }
```

**Expr** uses functions from `Env` for operator overloading. If types of 
operands match types of a function, the operator will be replaced with a 
function call.

* Next: [Visitor and Patch](Visitor-and-Patch.md)
