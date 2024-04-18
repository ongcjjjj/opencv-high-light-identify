# opencv-high-light-identify
#include <opencv2/opencv.hpp>
#include <iostream>

using namespace cv;
using namespace std;

void detectHighBeam(const Mat& image, const Mat& templateImage, double threshold = 0.8) {
    // 将图像转换为灰度图
    Mat grayImage;
    cvtColor(image, grayImage, COLOR_BGR2GRAY);

    // 使用模板匹配方法进行匹配
    Mat result;
    matchTemplate(grayImage, templateImage, result, TM_CCOEFF_NORMED);

    // 获取匹配位置
    vector<Point> locations;
    for (int y = 0; y < result.rows; ++y) {
        for (int x = 0; x < result.cols; ++x) {
            if (result.at<float>(y, x) >= threshold) {
                locations.push_back(Point(x, y));
            }
        }
    }

    // 在图像上标记远光灯位置
    for (const Point& pt : locations) {
        rectangle(image, pt, Point(pt.x + templateImage.cols, pt.y + templateImage.rows), Scalar(0, 255, 255), 2);
    }

    // 显示结果
    imshow("Result", image);
    waitKey(0);
    destroyAllWindows();
}

int main() {
    // 加载图像和模板
    Mat image = imread("car_image.jpg");
    Mat templateImage = imread("high_beam_template.jpg", IMREAD_GRAYSCALE);

    // 检查图像和模板是否成功加载
    if (image.empty() || templateImage.empty()) {
        cerr << "Error: Unable to load image or template." << endl;
        return -1;
    }

    // 调用远光检测函数
    detectHighBeam(image, templateImage);

    return 0;
}
