'''
识别手势点
根据食指指尖确定选定的键
根据大拇指与食指距离判断是否按下
使用说明：
    将大拇指移向食指表面为按下
    累计十个字符清零
    默认距离阈值为5000，即无需确认
    cvzone安装1.4.1
    opencv建议安装3.4版本，新版本没有cv2.flip()函数，捕捉的摄像头会存在镜像
'''
import cv2
from cvzone.HandTrackingModule import HandDetector
import cvzone
import time


cap = cv2.VideoCapture(0)
cap.set(3, 2560)
cap.set(4, 1920)
# 识别手势
detector = HandDetector(detectionCon=0.8)
#  定义按键类
class Button():
    def __init__(self, pos, text, size=[50, 50]):
        self.pos = pos
        self.text = text
        self.size = size
# 键盘关键字
keys = [['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'],
        ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L', ';'],
        ['Z', 'X', 'C', 'V', 'B', 'N', 'M', ',', '.', '/']]

buttonList = []
finalText = ''

for i in range(len(keys)):
    for j, key in enumerate(keys[i]):
        buttonList.append(Button([80 * j + 20, 100 + i * 80], key))


def draw_keyboard(img, buttonList):
    for button in buttonList:
        x, y = button.pos
        w, h = button.size
        cvzone.cornerRect(img, (x, y, w, h), 20, rt=0)
        cv2.rectangle(img, (x, y), (x + w, y + h), (255, 0, 255), cv2.FILLED)
        cv2.putText(img, button.text, (x + 10, y + 40),
                    cv2.FONT_HERSHEY_PLAIN, 3, (255, 255, 255), 2)
    return img


while True:
    success, img = cap.read()
    # 识别手势
    img = cv2.flip(img, 1)
    cv2.rectangle(img, (20, 350), (800, 500), (0, 255, 0), cv2.FILLED)
    img = detector.findHands(img)
    lmList = detector.findPosition(img)[0]
    img = draw_keyboard(img, buttonList)
    if lmList:
        for button in buttonList:
            x, y = button.pos
            w, h = button.size
            if x < lmList[8][0] < x + w and y < lmList[8][1] < y + h:
                cv2.rectangle(img, (x - 5, y - 5), (x + w + 5, y + h + 5), (175, 0, 175), cv2.FILLED)
                cv2.putText(img, button.text, (x + 10, y + 40),
                            cv2.FONT_HERSHEY_PLAIN, 3, (0, 0, 0), 2)
                if len(finalText) > 10:
                    finalText = ""
                # 根据食指和拇指距离判断，是否按下数字
                l, _, _ = detector.findDistance(8, 4, img, draw=False)
                # 默认不判断
                if l < 5000:
                    finalText += button.text
                    cv2.putText(img, finalText, (20, 465), cv2.FONT_HERSHEY_SIMPLEX, 3, (255, 255, 255), 4)
                    time.sleep(0.2)

    cv2.putText(img, finalText, (20, 465), cv2.FONT_HERSHEY_SIMPLEX, 3, (255, 255, 255), 4)
    cv2.imshow("Image", img)
    if cv2.waitKey(1) == ord('q'):
        break
