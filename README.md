# MPC_ruih_cppCode_Gen
This is used for generating the setup code for the MPC controller Arduino Library MPC_ruih
## Usage
To generate the setup code for MPC class in MPC_ruih library, you can use the python package `LinearMPCFactor` like the way in [main.py](https://github.com/rhrhhrhr/MPC_ruih_SolverSetup/blob/main/main.py):<br><br>
**code:**
```python
import numpy as np
import LinearMPCFactor as lMf

if __name__ == '__main__':
    A = np.array([[2, 1], [0, 2]])  # state space equation A 状态空间方程中的A
    B = np.array([[1, 0], [0, 1]])  # state space equation B 状态空间方程中的B
    Q = np.array([[1, 0], [0, 3]])  # cost function Q, which determines the convergence rate of the state 代价函数中的Q，决定了状态的收敛速度
    R = np.array([[1, 0], [0, 1]])  # cost function R, which determines the convergence rate of the input 代价函数中的R，决定了输入的收敛速度

    A_x = np.array([[1, 0], [-1, 0]])  # state constraints A_x @ x_k <= b_x 状态约束 A_x @ x_k <= b_x
    b_x = np.array([5, 5])

    A_u = np.array([[1, 0], [-1, 0], [0, 1], [0, -1]])  # input constraints A_u @ u_k <= b_u 输入约束 A_u @ u_k <= b_u
    b_u = np.array([1, 1, 1, 1])

    N = 5  # prediction horizon 预测区间

    mpc = lMf.LinearMPCFactor(A, B, Q, R, N, A_x, b_x, A_u, b_u)  # print the cpp code for initializing a MPC class 打印出初始化MPC类的cpp代码

    # mpc.decPlace = 4  # This is the number of decimal places reserved for matrix data, which defaults to 6 decimal places 这是矩阵数据保留的小数位数，默认保留小数点后6位
    e_V = 0.001  # tolerance of the error between optimal cost and real cost 实际代价函数的值与最优之间的最大误差
    e_g = 0.001  # tolerance of the violation of constraints 最大的违反约束的程度
    max_iter = 1000  # maximum steps of the solver 最大迭代步数

    mpc.PrintCppCode(e_V, e_g, max_iter)
```
**result:**
```cpp
MatDataType_t L_phi = 9.90287;
MatDataType_t e_V = 0.001;
MatDataType_t e_g = 0.001;
uint32_t max_iter = 1000;
uint32_t N = 5;

MatDataType_t A_arr[4] = {2, 1, 0, 2};
MatDataType_t B_arr[4] = {1, 0, 0, 1};
MatDataType_t Q_arr[4] = {1, 0, 0, 3};
MatDataType_t R_arr[4] = {1, 0, 0, 1};
MatDataType_t QN_arr[4] = {4.167039, 1.756553, 1.756553, 7.455801};
MatDataType_t F_arr[12] = {1.0, 0.0, -1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0};
MatDataType_t G_arr[12] = {0.0, 0.0, 0.0, 0.0, 1.0, 0.0, -1.0, 0.0, 0.0, 1.0, 0.0, -1.0};
MatDataType_t c_arr[6] = {5, 5, 1, 1, 1, 1};
MatDataType_t FN_arr[8] = {1.583519, 0.878277, -1.583519, -0.878277, 0.086517, 1.788762, -0.086517, -1.788762};
MatDataType_t cN_arr[4] = {1.0, 1.0, 1.0, 1.0};

Matrix A = Matrix(2, 2, A_arr);
Matrix B = Matrix(2, 2, B_arr);
Matrix Q = Matrix(2, 2, Q_arr);
Matrix R = Matrix(2, 2, R_arr);
Matrix QN = Matrix(2, 2, QN_arr);
Matrix F = Matrix(6, 2, F_arr);
Matrix G = Matrix(6, 2, G_arr);
Matrix c = Matrix(6, 1, c_arr);
Matrix FN = Matrix(4, 2, FN_arr);
Matrix cN = Matrix(4, 1, cN_arr);

MPCController mpc = MPCController(L_phi, e_V, e_g, max_iter, N, A, B, Q, R, QN, F, G, c, FN, cN);
```
## Libraries
Package LinearMPCFactor uses numpy and scipy. The versions are as below:<br>
numpy==1.22.4<br>
scipy==1.7.3
