# Khắc phục cấu hình GitLab CI/CD cho User Service

## Các vấn đề trong cấu hình ban đầu

1. **Chưa khai báo môi trường chạy:** Không có `image`, nên job phụ thuộc vào môi trường đã được cấu hình trên GitLab Runner. Môi trường đó có thể không cài Java/JDK 17; khi đó Gradle Wrapper không thể khởi chạy vì thiếu Java. Việc không chỉ định image cũng không bảo đảm có đúng phiên bản JDK mà dự án yêu cầu.
2. **Khai báo `stages` sai kiểu dữ liệu:** `stages` của GitLab CI phải là một danh sách (sequence). `build_app` hiện được khai báo như một giá trị đơn, thay vì phần tử danh sách, nên cấu hình không đáp ứng schema của GitLab CI.

## Cấu hình đã sửa

Tệp `.gitlab-ci.yml` dùng image `gradle:8.7-jdk17`, khai báo `build_app` thành một phần tử trong danh sách `stages`, và chạy lệnh build bằng Gradle Wrapper:

```yaml
image: gradle:8.7-jdk17

stages:
  - build_app

build_job:
  stage: build_app
  script:
    - chmod +x ./gradlew
    - ./gradlew clean build -x test
```

Lệnh `chmod +x ./gradlew` đảm bảo wrapper có quyền thực thi trên runner Linux. Lệnh build giữ nguyên yêu cầu ban đầu và bỏ qua test với tùy chọn `-x test`.
