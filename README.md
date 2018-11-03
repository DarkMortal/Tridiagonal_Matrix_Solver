# tridiagonal

`tridiagonal` is a C++ template library with solvers for tridiagonal block matrix systems.
## Solvers 
`tridiagonal::solve_tridiagonal` solves block matrix systems on the form

<p align="center">
<img src="resources/tridiagonal.png" width="500">
</p>

`tridiagonal::solve_off_tridiagonal` solves block matrix systems on the form

<p align="center">
<img src="resources/off_tridiagonal.png" width="500">
</p>


## Installation

[Eigen](http://eigen.tuxfamily.org) is used for linear algebra structures and operations.

To install the library clone the repository and copy the [tridiagonal](tridiagonal/) directory to the desired install location. 

## Example

```cpp
#include <eigen3/Eigen/Dense>
#include <tridiagonal/tridiagonal>

int main(){

    Eigen::Matrix<double, 6, 6> M;

    M <<    0, 3,  1, 2,  0, 0,
            2, 1,  4, 5,  0, 0,

            6, 7,  1, 2,  4, 5,
            1, 3,  9, 0,  2, 3,

            0, 0,  2, 1,  5, 2,
            0, 0,  3, 7,  9, 3;


    Eigen::Matrix<double, 6, 1> D;
    D <<    0,
            2,
            3,
            1,
            -9,
            2;

    Eigen::Matrix2d a1, a2, b0, b1, b2, c0, c1;
    b0 = M.block(0, 0, 2, 2);
    b1 = M.block(2, 2, 2, 2);
    b2 = M.block(4, 4, 2, 2);

    c0 = M.block(0, 2, 2, 2);
    c1 = M.block(2, 4, 2, 2);

    a1 = M.block(2, 0, 2, 2);
    a2 = M.block(4, 2, 2, 2);

    Eigen::Vector2d d0, d1, d2;
    d0 = D.block(0, 0, 2, 1);
    d1 = D.block(2, 0, 2, 1);
    d2 = D.block(4, 0, 2, 1);

    vector<Eigen::Matrix2d> diagonal = {b0, b1, b2};
    vector<Eigen::Matrix2d> lower_diagonal = {a1, a2};
    vector<Eigen::Matrix2d> upper_diagonal = {c0, c1};
    vector<Eigen::Vector2d> rhs = {d0, d1, d2};

    vector<Eigen::Vector2d> solution = tridiagonal::solve_tridiagonal(lower_diagonal,
                                                                      diagonal,
                                                                      upper_diagonal,
                                                                      rhs);

    Eigen::Matrix<double, 6, 1> tridiagonal_solution;
    tridiagonal_solution.block(0, 0, 2, 1) = solution[0];
    tridiagonal_solution.block(2, 0, 2, 1) = solution[1];
    tridiagonal_solution.block(4, 0, 2, 1) = solution[2];

    Eigen::Matrix<double, 6, 1> eigen_solution = M.fullPivHouseholderQr().solve(D);

    assert(tridiagonal_solution.isApprox(eigen_solution));
}
```

## Tests

The solvers are unit tested with [Catch2](https://github.com/catchorg/Catch2). To compile and run the unit tests make sure that [tridiagonal](tridiagonal/), Eigen and Catch2 is in the include path and run

```bash
$ gcc tests/*.cpp -o test.out -std=c++11
$ ./test.out
```
