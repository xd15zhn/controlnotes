@[TOC]
# 画图函数
```python
import numpy as np
import matplotlib.pyplot as plt
def plot_bode(magnitude, amplitude, phase):
    amplitude = 20*np.log10(amplitude)
    fig = plt.figure()
    fig.subplots_adjust(hspace=0.1, top=0.99, bottom=0.05, left=0.06, right=0.99)
    ax1 = fig.add_subplot(211)
    ax1.grid(True)
    ax1.plot(magnitude, amplitude)
    ax2 = fig.add_subplot(212)
    ax2.plot(magnitude, phase)
    ax2.set_yticks(np.linspace(180, -360, 13))
    ax2.set_ylim(phase.min()-10, phase.max()+10)
    ax2.grid(True)
    plt.show()
if __name__ == '__main__':
    index = np.linspace(-2, 3, 41)
    omega = np.power(10, index)
    phi = -(np.pi/2+np.arctan2(0.1*omega, 1)+np.arctan2(0.2*omega, 1))*180/np.pi
    amp = 30/(omega*np.sqrt(1+(0.1*omega)**2)*np.sqrt(1+(0.2*omega)**2))
    amp = 20*np.log10(amp)
    plot_bode(index, amp, phi)
```
# 一阶系统
$$G(s)=\frac{K}{s+a}$$
```python
import numpy as np
from zhnbodeplot import plot_bode
a = 10
K = 10
index = np.linspace(-1, 3, 41)
omega = np.power(10, index)
phi = -(np.pi/2+np.arctan2(0.1*omega, 1)+np.arctan2(0.2*omega, 1))*180/np.pi
amp = 30/(omega*np.sqrt(1+(0.1*omega)**2)*np.sqrt(1+(0.2*omega)**2))
plot_bode(index, amp, phi)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ff565ec71b844824bd6b382600f7761d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)

# 二阶系统
$$G(s)=\frac{\omega_n^2}{s^2+2\xi\omega_ns+\omega_n^2}$$
```python
import numpy as np
from zhnbodeplot import plot_bode
omegan = 10
xi = 0.5
index = np.linspace(-1, 3, 41)
omega = np.power(10, index)
amp = omegan**2/np.sqrt((omegan**2 - omega**2)**2 + (2*xi*omegan*omega)**2)
amp = 20*np.log10(amp)
phi = -np.arctan2(2*xi*omegan*omega, omegan**2 - omega**2)
phi *= 180/np.pi
plot_bode(index, amp, phi)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ad862e0e713049a6af5799065aa2b6a5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
# 时滞系统
$$G(s)=\frac{2\text{e}^{-\tau s}}{s+1}$$
```python
import numpy as np
from zhnbodeplot import plot_bode
tau = 2*np.pi/3/np.sqrt(3)
index = np.linspace(-1, 0.5, 41)
omega = np.power(10, index)
amp = 2/np.sqrt(omega**2 + 1)
phi = -omega*tau-np.arctan2(omega, 1)
amp = 20*np.log10(amp)
phi *= 180/np.pi
plot_bode(index, amp, phi)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9b2b071dfd1747d49e00d3cd1da3c462.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
# 例
$$G(s)=\frac{30}{s(1+0.1s)(1+0.2s)}$$
```python
phi = -(np.pi/2+np.arctan2(0.1*omega, 1)+np.arctan2(0.2*omega, 1))*180/np.pi
amp = 30/(omega*np.sqrt(1+(0.1*omega)**2)*np.sqrt(1+(0.2*omega)**2))
```
# PID
$$G(s)=K_p+\frac{K_i}{s}+K_ds$$
```python
Kp = 5
Ki = 1
Kd = 0.01
Re1 = Kp
Im1 = Kd*omega - Ki/omega
phi = np.arctan2(Im1, Re1)*180/np.pi
amp = np.sqrt(Re1**2 + Im1**2)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/7955eb1017284ec08de8bb62325f83a1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)

# 带滤波的PID
$$
G(s)=K_p+\frac{K_i}{s}+\frac{K_ds}{s+N}=\frac{(K_p+K_d)s^2+(K_pN+K_i)s+K_iN}{s^2+Ns} \\
=\frac{K_iN-(K_p+K_d)\omega^2+j\omega(K_pN+K_i)}{-\omega^2+jN\omega}
$$
```python
Kp = 5
Ki = 1
Kd = 0.01
N = 100
Re1 = Ki*N-(Kp+Kd)*omega**2
Im1 = omega*(Kp*N+Ki)
Re2 = -omega**2
Im2 = N*omega
phi = np.arctan2(Im1, Re1)*180/np.pi
phi -= np.arctan2(Im2, Re2)*180/np.pi
amp = np.sqrt(Re1**2 + Im1**2)
amp /= np.sqrt(Re2**2 + Im2**2)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/bd6606690b7549ff88636c01b169aca0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_17,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)

# 二阶系统的谐振频率和峰值
$$A=\frac{\omega_n^2}{\sqrt{(\omega_n^2-\omega^2)^2+(2\xi\omega_n\omega)^2}}$$
令$x=\omega^2$，$a=\omega_n^2$，$k+2=4\xi^2$，
$$f(x)=x^2-2ax+a^2+(k+2)ax$$
$f(x)$最小值
$$f(-\frac{ka}{2})=a^2-\frac{k^2a^2}{4}$$
$x>0$，$a>0$，所以$k<0$，即$4\xi^2-2<0$
$$\omega_r=\sqrt{-\frac{\omega_n^2(4\xi^2-2)}{2}}=\omega_n\sqrt{1-2\xi^2}$$
$$\frac{1}{M_r}=\frac{1}{\omega_n^2}\sqrt{\omega_n^4(1-\frac{(4\xi^2-2)^2}{4})}=\sqrt{1-(2\xi-1)^2}=2\xi\sqrt{1-\xi^2}$$
$4x^2(1-x^2)\le 1$，所以谐振峰值总是≥1。
# 二阶系统的幅相曲线
$$G(s)=\frac{\omega_n^2}{s(s+2\xi\omega_n)}$$
```python
import matplotlib.pyplot as plt
import numpy as np
fig = plt.figure()
ax = plt.axes(projection='polar')

index = np.linspace(-1, 3, 41)
radius2 = np.power(10, index)
theta2 =  np.pi/2 * np.ones(radius2.shape)
theta1 = np.linspace(-np.pi/2, np.pi/2, 100)
radius1 =  radius2[0] * np.ones(theta1.shape)
theta3 = np.linspace(np.pi/2, -np.pi/2, 100)
radius3 =  radius2[-1] * np.ones(theta1.shape)
stheta = np.concatenate([theta1, theta2, theta3, -theta2], axis=0)
sradius = np.concatenate([radius1, radius2, radius3, radius2[::-1]], axis=0)
# stheta = np.concatenate([-theta2], axis=0)
# sradius = np.concatenate([radius2], axis=0)

xi = 0.5
omegan = 10
x = sradius * np.cos(stheta) + 2*xi*omegan
y = sradius * np.sin(stheta)
gradius = sradius * np.sqrt(x**2 + y**2)
gradius = omegan**2 / gradius
gtheta = -stheta - np.arctan2(y, x)
# gradius = 20 * np.log10(gradius)
# mask = gradius < -70
# gradius = gradius[mask]
# gtheta = gtheta[mask]
ax.plot(gtheta, gradius)
plt.show()
```
整体分成4段，第一段是半径极小的半圆，第二段是虚轴上的0到正无穷，第三段是无穷大的半圆，第四段是虚轴上的负无穷到0。幅相曲线整体见下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/b6331414dc2e46e39c214e35b1d84f46.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
第二段见下图（注意幅度，下图是整体图的局部，下同）：
![在这里插入图片描述](https://img-blog.csdnimg.cn/fb9cea622c794fbe8a79f29ebc8fac0d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
第三段见下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/1371cc4541a64687abc2e4196d055d18.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
注意第三段有个不合常理的地方，那就是里面那个圆的顺序是逆时针，也就是说整个曲线并没有交叉，大概是下面这样：
![在这里插入图片描述](https://img-blog.csdnimg.cn/7b97431c1909426b94b2799bd4032418.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_7,color_FFFFFF,t_70,g_se,x_16#pic_center =25%x25%)
如果$\xi<0$，整体图是下面这样：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c06274573f0941788a6fe8eb3703511f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)

