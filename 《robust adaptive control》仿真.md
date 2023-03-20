# 例4.2.1
系统方程$y=\theta u$
$\epsilon=y-\hat\theta u$
$\dot{\hat\theta}=-\gamma\epsilon u$
```cpp
#include "simucpp.hpp"
using namespace simucpp;
using namespace std;
int main()
{
    Simulator sim1(10);
    FMInput(mdu, &sim1);  // u
    FMGain(mdy, &sim1);  // 实际输出,实际θ值
    FMProduct(prod1, &sim1);
    FMProduct(prod2, &sim1);
    FMSum(mderr, &sim1);  // ε,预测误差
    FMIntegrator(mdpretheta, &sim1);  // 预测参数
    FMOutput(out1, &sim1);
    double gamma = 2;  // γ,预测增益
    mdu->Set_InputFunction([](double t){return sin(t);});
    mdy->Set_Gain(2.5);  // θ=2.5
    sim1.connect(mdu, mdy);  // y=θ*u
    sim1.connect(mdu, prod1);  // ε=y-θp*u
    sim1.connect(mdpretheta, prod1);  // ε=y-θp*u
    sim1.connect(mdy, mderr);  // ε=y-θp*u
    sim1.connect(prod1, mderr);  // ε=y-θp*u
    mderr->Set_InputGain(-1);  // ε=y-θp*u
    sim1.connect(mdu, prod2);  // θ'=γ*ε*u
    sim1.connect(mderr, prod2);  // θ'=γ*ε*u
    prod2->Set_InputGain(gamma);  // θ'=γ*ε*u
    sim1.connect(prod2, mdpretheta);  // θ=∫θdt
    sim1.connect(mdpretheta, out1);
    sim1.Initialize();
    sim1.Simulate();
    return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/f242cdf203c6474b8b1a88adcf34a186.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
# 例4.2.2
系统方程$x'=-ax+bu$
$\hat a'=-\epsilon\hat x$
$\hat b'=\epsilon u$
$\hat x'=-\hat a\hat x+\hat bu$
```cpp
#include "simucpp.hpp"
int main()
{
    using namespace simucpp;
    using namespace std;
    Simulator sim1(100);
    TransferFcn *mdx = new TransferFcn(&sim1, vector<double>{1}, vector<double>{1, 2});
    FMInput(mdu, &sim1);  // u
    FMProduct(mdproda, &sim1);  // a' = -ε*xp
    FMProduct(mdprodb, &sim1);  // b' = ε*u
    FMFcnMISO(mdmisox, &sim1);  // x' = -a*x+b*u
    FMSum(mderrtemp, &sim1);  // 预测误差ε
    FMGain(mderr, &sim1);  // 预测增益γ
    FMIntegrator(mdprea, &sim1);  // a的预测值ap
    FMIntegrator(mdpreb, &sim1);  // b的预测值bp
    FMIntegrator(mdprex, &sim1);  // x的预测值xp
    mdu->Set_InputFunction([](double t){return sin(t);});
    mdmisox->Set_Function([](double *x){return -x[0]*x[1]+x[2]*x[3];});  // x' = -a*x+b*u
    mderr->Set_Gain(5);  // γ=5
    sim1.connect(mdu, mdx);  // x' = -a*x + b*u
    sim1.connect(mdx, mderrtemp);  // ε = x - xp
    sim1.connect(mdprex, mderrtemp);  // ε = x - xp
    mderrtemp->Set_InputGain(-1);  // ε = x - xp
    sim1.connect(mderrtemp, mderr);  // ε *= gamma
    sim1.connect(mdprex, mdproda);  // a' = -ε*xp
    sim1.connect(mderr, mdproda);  // a' = -ε*xp
    mdproda->Set_InputGain(-1);  // a' = -ε*xp
    sim1.connect(mderr, mdprodb);  // b' = ε*u
    sim1.connect(mdu, mdprodb);  // b' = ε*u
    sim1.connect(mdprea, mdmisox);  // x' = -a*x+b*u
    sim1.connect(mdprex, mdmisox);  // x' = -a*x+b*u
    sim1.connect(mdpreb, mdmisox);  // x' = -a*x+b*u
    sim1.connect(mdu, mdmisox);  // x' = -a*x+b*u
    sim1.connect(mdproda, mdprea);
    sim1.connect(mdprodb, mdpreb);
    sim1.connect(mdmisox, mdprex);
    FMOutput(outa, &sim1);
    FMOutput(outb, &sim1);
    outa->Set_SampleTime(0.2);
    outb->Set_SampleTime(0.2);
    sim1.connect(mdprea, outa);
    sim1.connect(mdpreb, outb);
    sim1.Initialize();
    sim1.Simulate();
    return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ba09a7bd64214a6f82cd766a9b8b86f9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)

