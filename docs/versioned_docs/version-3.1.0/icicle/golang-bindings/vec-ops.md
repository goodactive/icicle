# Vector Operations

## Overview

Icicle exposes a number of vector operations which a user can use:

* The VecOps API provides efficient vector operations such as addition, subtraction, and multiplication, supporting both single and batched operations.
* MatrixTranspose API allows a user to perform a transpose on a vector representation of a matrix, with support for batched transpositions.

## VecOps API Documentation

### Example

#### Vector addition

```go
package main

import (
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/core"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/curves/bn254"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/curves/bn254/vecOps"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/runtime"
)

func main() {
	testSize := 1 << 12
	a := bn254.GenerateScalars(testSize)
	b := bn254.GenerateScalars(testSize)
	out := make(core.HostSlice[bn254.ScalarField], testSize)
	cfg := core.DefaultVecOpsConfig()

	// Perform vector multiplication
	err := vecOps.VecOp(a, b, out, cfg, core.Add)
	if err != runtime.Success {
		panic("Vector addition failed")
	}
}
```

#### Vector Subtraction

```go
package main

import (
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/core"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/curves/bn254"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/curves/bn254/vecOps"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/runtime"
)

func main() {
	testSize := 1 << 12
	a := bn254.GenerateScalars(testSize)
	b := bn254.GenerateScalars(testSize)
	out := make(core.HostSlice[bn254.ScalarField], testSize)
	cfg := core.DefaultVecOpsConfig()

	// Perform vector multiplication
	err := vecOps.VecOp(a, b, out, cfg, core.Sub)
	if err != runtime.Success {
		panic("Vector subtraction failed")
	}
}
```

#### Vector Multiplication

```go
package main

import (
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/core"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/curves/bn254"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/curves/bn254/vecOps"
	"github.com/ingonyama-zk/icicle/v3/wrappers/golang/runtime"
)

func main() {
	testSize := 1 << 12
	a := bn254.GenerateScalars(testSize)
	b := bn254.GenerateScalars(testSize)
	out := make(core.HostSlice[bn254.ScalarField], testSize)
	cfg := core.DefaultVecOpsConfig()

	// Perform vector multiplication
	err := vecOps.VecOp(a, b, out, cfg, core.Mul)
	if err != runtime.Success {
		panic("Vector multiplication failed")
	}
}
```

### VecOps Method

```go
func VecOp(a, b, out core.HostOrDeviceSlice, config core.VecOpsConfig, op core.VecOps) (ret runtime.EIcicleError)
```

#### Parameters

- **`a`**: The first input vector.
- **`b`**: The second input vector.
- **`out`**: The output vector where the result of the operation will be stored.
- **`config`**: A `VecOpsConfig` object containing various configuration options for the vector operations.
- **`op`**: The operation to perform, specified as one of the constants (`Sub`, `Add`, `Mul`) from the `VecOps` type.

#### Return Value

- **`EIcicleError`**: A `runtime.EIcicleError` value, which will be `runtime.Success` if the operation was successful, or an error if something went wrong.

### VecOpsConfig

The `VecOpsConfig` structure holds configuration parameters for the vector operations, allowing customization of its behavior.

```go
type VecOpsConfig struct {
	StreamHandle     runtime.Stream
	isAOnDevice      bool
	isBOnDevice      bool
	isResultOnDevice bool
	IsAsync          bool
	batch_size       int
	columns_batch    bool
	Ext              config_extension.ConfigExtensionHandler
}
```

#### Fields

- **`StreamHandle`**: Specifies the stream (queue) to use for async execution.
- **`isAOnDevice`**: Indicates if vector `a` is located on the device.
- **`isBOnDevice`**: Indicates if vector `b` is located on the device.
- **`isResultOnDevice`**: Specifies where the result vector should be stored (device or host memory).
- **`IsAsync`**: Controls whether the vector operation runs asynchronously.
- **`batch_size`**: Number of vectors (or operations) to process in a batch. Each vector operation will be performed independently on each batch element.
- **`columns_batch`**: true if the batched vectors are stored as columns in a 2D array (i.e., the vectors are strided in memory as columns of a matrix). If false, the batched vectors are stored contiguously in memory (e.g., as rows or in a flat array).
- **`Ext`**: Extended configuration for backend.

#### Default Configuration

Use `DefaultVecOpsConfig` to obtain a default configuration, customizable as needed.

```go
func DefaultVecOpsConfig() VecOpsConfig
```

## MatrixTranspose API Documentation

This section describes the functionality of the `TransposeMatrix` function used for matrix transposition.

The function takes a matrix represented as a 1D slice and transposes it, storing the result in another 1D slice.

If VecOpsConfig specifies a batch_size greater than one, the transposition is performed on multiple matrices simultaneously, producing corresponding transposed matrices. The storage arrangement of batched matrices is determined by the columns_batch field in the VecOpsConfig.

### Function

```go
func TransposeMatrix(in, out core.HostOrDeviceSlice, columnSize, rowSize int, config core.VecOpsConfig) runtime.EIcicleError
```

## Parameters

- **`in`**: The input matrix is a `core.HostOrDeviceSlice`, stored as a 1D slice.
- **`out`**: The output matrix is a `core.HostOrDeviceSlice`, which will be the transpose of the input matrix, stored as a 1D slice.
- **`columnSize`**: The number of columns in the input matrix.
- **`rowSize`**: The number of rows in the input matrix.
- **`config`**: A `VecOpsConfig` object containing various configuration options for the vector operations.

## Return Value

- **`EIcicleError`**: A `runtime.EIcicleError` value, which will be `runtime.Success` if the operation was successful, or an error if something went wrong.

## Example Usage

```go
var input = make(core.HostSlice[ScalarField], 20)
var output = make(core.HostSlice[ScalarField], 20)

// Populate the input matrix
// ...

// Get device context
cfg, _ := runtime.GetDefaultDeviceContext()

// Transpose the matrix
err := TransposeMatrix(input, output, 5, 4, cfg)
if err != runtime.Success {
    // Handle the error
}

// Use the transposed matrix
// ...
```

In this example, the `TransposeMatrix` function is used to transpose a 5x4 matrix stored in a 1D slice. The input and output slices are stored on the host (CPU), and the operation is executed synchronously.
