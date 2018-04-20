# Xây dựng logic app với Computer Vision API
---
## Mục tiêu
Trong lab này chúng ta sẽ xây dựng một logic app đơn giản để phân tích hình ảnh với yêu cầu như sau
- Logic app được khởi chạy khi có một hình ảnh được upload lên Blob Storage
- Logic app sau đó sẽ phân tích hình ảnh, gửi dữ liệu phân tích được vào Queue trong service bus
- Logic app trích xuất văn bản ở trong hình ảnh, nếu có văn bản thì phân tích và trích xuất từ khóa có trong văn bản

## Một số yêu cầu
Để hoàn thành lab này, bạn cần có:
- Azure subscription, đăng ký với GAB2018 organizer để có Azure Pass, sau đó đăng nhập vào Azure Portal
- cài đặt Azure SDK
- cài đặt Visual Studio 2017 và logic app designer extension
- cài đặt Microsoft Azure Storage Explorer

## Tạo các dịch vụ
Trong lab này chúng ta cần phải dùng những dịch vụ sau: Computer Vision API, Text Analytics API, Storage Account, Azure Service Bus
Đăng nhập vào Azure Portal, và làm theo hướng dẫn trong portal để tạo các dịch vụ nêu trên.

## design logic app
Trong lab này chúng ta sẽ xây dựng logic app sử dụng visual studio. 
### Tạo Azure Resource Group project
### Tạo trigger
Trigger là step khởi chạy logic app, theo yêu cầu chúng ta sẽ tạo trigger dựa trên Blob Storage connector và sự kiện một file hình ảnh được upload lên container trong Blob Storage Account.
### Tạo Analyze Image action
Analyze Image là một action trong Computer Vision connector. Khi tạo ra action này bạn cần cung cấp thông tin về connection name, API Key, và URL của Computer Vision API. Sau khi connection được tạo, bạn cần cung cấp hình ảnh cho action, bạn nhập vào URL của image được upload lên blob.
### Tạo 'Send Message' action
'Send Message' action được sử dụng để gửi dữ liệu đã được phân tích vào Queue trong Azure Service Bus. Bạn cần input thông tin về Azure Service Bus connection để tạo ra Send Message action
### Tạo 'Optical Character Recognition (OCR) to text' action
Thực hiện các step tương tự như bươc tạo Analyze Image action
### Tạo If-Then flow control
Nếu có văn bản thì tạo các Text Sentiment, Keyword extraction actions
