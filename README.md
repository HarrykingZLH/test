# Bezier曲线绘制

## 介绍

这段代码实现了Bezier曲线的绘制功能。Bezier曲线是一种用于平滑插值和生成曲线的数学工具，广泛应用于图形设计和计算机图形学领域。该代码使用了OpenCV库中的一些功能，用于在给定的控制点集合上绘制Bezier曲线。

## 函数

### `bool recursive_bezier(const std::vector<cv::Point2f>& control, std::vector<cv::Point2f>& p1, std::vector<cv::Point2f>& p2, float t, cv::Mat& window)`

这个函数用于递归地计算Bezier曲线上的点。它接受以下参数：

- `control`：Bezier曲线的控制点集合。
- `p1`：当前Bezier曲线上的点。
- `p2`：下一次迭代中的Bezier曲线上的点。
- `t`：曲线参数，决定曲线上的点位置。
- `window`：用于绘制的图像窗口。

### `void bezier(const std::vector<cv::Point2f>& control, cv::Mat& window)`

这个函数用于绘制Bezier曲线。它接受以下参数：

- `control`：Bezier曲线的控制点集合。
- `window`：用于绘制的图像窗口。

## 代码解释
```c++
bool recursive_bezier(const std::vector<cv::Point2f>& control, std::vector<cv::Point2f>& p1, std::vector<cv::Point2f>& p2, float t, cv::Mat& window) {
    int n = p1.size();  // 获取当前Bezier曲线上点的数量
    double r = 1;       // 初始化r为1，它将用于颜色强度的计算

    if (n == 1) {  // 如果只有一个点在当前Bezier曲线上
        for (int i = -1; i <= 1; i++) {  // 遍历一个3x3的格子
            for (int j = -1; j <= 1; j++) {
                int y = p1[0].y + j;  // 计算当前格子的y坐标
                int x = p1[0].x + i;  // 计算当前格子的x坐标

                // 如果坐标超出窗口边界，则跳过
                if (y > 700 || y < 0 || x > 700 || x < 0) {
                    continue;
                }

                // 根据点到中心的距离计算r值，用于颜色强度调整
                r = 1 - sqrt(2) * sqrt(pow(p1[0].y - y - 0.5, 2) + pow(p1[0].x - x - 0.5, 2)) / 3;

                // 修改窗口上像素的颜色，绘制Bezier曲线
                window.at<cv::Vec3b>(y, x)[1] = std::fmax(window.at<cv::Vec3b>(y, x)[1], 255 * r);
            }
        }

        p2 = control;  // 将控制点集合复制给p2
        p1.clear();    // 清空p1
        return true;   // 返回true，表示完成曲线绘制
    }

    for (int i = 0; i < n - 1; i++) {  // 如果有多个点在当前Bezier曲线上
        p2.push_back((1 - t) * p1[i] + t * p1[i + 1]);  // 计算Bezier曲线上的点
    }

    p1.clear();  // 清空p1
    return false;  // 返回false，表示需要进行下一次递归迭代
}


void bezier(const std::vector<cv::Point2f>& control, cv::Mat& window)
{
    std::vector<cv::Point2f> current = control, next;  // 初始化current为控制点集合，next为空向量
    bool flag1, flag2;  // 初始化两个标志位flag1和flag2，用于控制循环

    flag1 = true;  // 初始化flag1为true

    for (double t = 0.0; t <= 1.0; t += 0.001)  // 遍历Bezier曲线的参数t从0到1
    {
        flag2 = false;  // 初始化flag2为false，用于追踪内部循环的状态

        while (!flag2)  // 在flag2变为true之前循环
        {
            flag2 = recursive_bezier(control, current, next, t, window);  // 调用递归函数计算Bezier曲线

            std::swap(current, next);  // 交换current和next以准备下一次迭代
            flag1 = !flag1;  // 切换flag1的状态
        }
    }
}

```
