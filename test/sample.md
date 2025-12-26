# c++数字图像处理
## 1.图像灰度变换
- 相关代码
```
#include<opencv2/opencv.hpp>
#include<iostream>
using namespace cv;
using namespace std;

Mat manualGrayscaleWeighted(const Mat& colorImage) {
    // 获取图像尺寸
    int rows = colorImage.rows;
    int cols = colorImage.cols;

    // 检查输入图像是否为彩色图像（3通道）
    if (colorImage.channels() != 3) {
        cout << "警告：输入图像不是彩色图像，直接返回原图" << endl;
        return colorImage.clone();
    }

    // 创建灰度图像矩阵
    // CV_8UC1 表示8位无符号单通道图像
    Mat grayImage(rows, cols, CV_8UC1);

    // 遍历每个像素
    for (int i = 0; i < rows; i++) {
        // 获取当前行的指针（提高访问效率）
        const uchar* colorRow = colorImage.ptr<uchar>(i);
        uchar* grayRow = grayImage.ptr<uchar>(i);

        for (int j = 0; j < cols; j++) {
            // OpenCV默认使用BGR顺序
            // colorRow[j*3] 对应蓝色通道(B)
            // colorRow[j*3+1] 对应绿色通道(G)
            // colorRow[j*3+2] 对应红色通道(R)
            uchar blue = colorRow[j * 3];
            uchar green = colorRow[j * 3 + 1];
            uchar red = colorRow[j * 3 + 2];

            // 计算灰度值 - 使用加权平均法
            // 浮点数运算，最后转换为uchar类型
            // 使用整型运算提高效率：Gray = (299*R + 587*G + 114*B) / 1000
            int grayValue = (299 * red + 587 * green + 114 * blue) / 1000;

            // 确保像素值在0-255范围内
            if (grayValue > 255) grayValue = 255;
            if (grayValue < 0) grayValue = 0;

            // 将灰度值赋给灰度图像
            grayRow[j] = static_cast<uchar>(grayValue);
        }
    }

    return grayImage;
}

int main()
{
	Mat Image = imread("1.jpg");
	imshow("原始彩色图像", Image);
    cout << "图像尺寸: " << Image.cols << "x" << Image.rows << endl;
    cout << "图像通道数: " << Image.channels() << endl;

    Mat weightedGray = manualGrayscaleWeighted(Image);
    imshow("加权平均法", weightedGray);
    //imwrite("gray.jpg", weightedGray);

	waitKey(0);
	return 0;
}

```