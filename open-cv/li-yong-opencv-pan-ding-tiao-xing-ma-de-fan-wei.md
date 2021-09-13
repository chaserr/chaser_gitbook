# 利用OpenCV判定条形码的范围

```c
//
//  main.cpp
//  barcode
//
//  Created by BlueCocoa on 15/8/8.
//  Copyright © 2015 0xBBC. All rights reserved.
//

#include <iostream>
#include <opencv2/opencv.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <vector>

using namespace cv;
using namespace std;

/**
 *  @brief 找出面积最大的轮廓, 保存在max_contour里
 */
void find_max_area(InputArray contour);
_InputArray max_contour;

void find_max_area(InputArray contour) {
    static double area = -UINT_MAX;
    double now = contourArea(contour);
    if (now >= area) {
        area = now;
        max_contour = contour;
    }
}

int main(int argc, const char * argv[]) {
    Mat barcode;
    Mat gray, grayX, grayY;
    Mat gradient;
    Mat blurred;
    Mat thresh, kernel, closed;
    vector<vector<Point>> contours;
    vector<Point> c;
    vector<vector<Point>> barcode_area;

    // 读取图像, 转为灰度图
    barcode = imread("barcode.jpg");
    if (!barcode.data) {
        fprintf(stderr, "imread failed!\n");
        exit(EXIT_FAILURE);
    }
    cvtColor(barcode, gray, COLOR_BGR2GRAY);

    // 分别计算X, Y方向上的梯度
    Sobel(gray, grayX, CV_32F, 1, 0, -1);
    Sobel(gray, grayY, CV_32F, 0, 1, -1);

    // 从X中减去Y
    subtract(grayX, grayY, gradient);
    convertScaleAbs(gradient, gradient);

    // 模糊
    blur(gradient, blurred, Size_<int>(9, 9));

    // 主要留下白色
    threshold(blurred, thresh, 225, 255, THRESH_BINARY);

    // 建立闭区域
    kernel = getStructuringElement(MORPH_RECT, Size_<int>(21, 7));
    morphologyEx(thresh, closed, MORPH_CLOSE, kernel);
    // 腐蚀并扩大范围
    erode(closed, closed, NULL, Point(-1,-1), 4);
    dilate(closed, closed, NULL, Point(-1,-1), 4);

    // 查找轮廓
    findContours(closed, contours, RETR_EXTERNAL, CHAIN_APPROX_SIMPLE);

    // 找出最大的那个
    for_each(contours.begin(), contours.end(), find_max_area);

    // 取rect
    RotatedRect rect = minAreaRect(max_contour);
    vector<vector<Point> > barcode_area;
    Point2f vertices[4];
    vector<Point> c1;
    rect.points(vertices);
    c1.push_back(vertices[0]);
    c1.push_back(vertices[1]);
    c1.push_back(vertices[2]);
    c1.push_back(vertices[3]);
    barcode_area.push_back(c1);

    // 在barcode上画出轮廓
    drawContours(barcode, barcode_area, -1, Scalar(0, 255, 0), 3);

    // 在Window中显示
    namedWindow("Barcode", CV_WINDOW_AUTOSIZE);
    imshow("Barcode", barcode);

    waitKey(0);
    return 0;
}
```

