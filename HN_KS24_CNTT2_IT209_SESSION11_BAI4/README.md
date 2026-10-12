# Bài 4: Khai báo GitHub Secrets và cấu hình Đăng nhập Container Registry

## 1. Mục tiêu
- Tạo tệp mã hóa an toàn (**Repository Secret**) lưu trữ Personal Access Token (PAT) cá nhân trên GitHub.
- Cấu hình bước xác thực đăng nhập tự động vào **GitHub Container Registry (GHCR - `ghcr.io`)** trong quy trình CI bằng `docker/login-action@v3`.

---

## 2. Cấu trúc thư mục dự án

```text
SESSION11_BAI4/
├── .github/
│   └── workflows/
│       └── ci.yml                  # Kịch bản GitHub Actions CI (có bước docker/login-action@v3)
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── homework/
│   └── session_11/
│       └── ex4/                    # Thư mục sản phẩm nộp bài theo đường dẫn gợi ý
│           ├── .github/
│           │   └── workflows/
│           │       └── ci.yml
│           └── README.md           # Hướng dẫn thiết lập Secret & chụp ảnh nộp bài
├── src/
│   ├── main/java/com/example/
│   │   ├── Application.java
│   │   └── Calculator.java
│   └── test/java/com/example/
│       └── CalculatorTest.java
├── .gitattributes
├── .gitignore
├── build.gradle
├── gradlew
├── gradlew.bat
├── settings.gradle
└── README.md
```

---

## 3. Nội dung tệp kịch bản `.github/workflows/ci.yml`

```yaml
name: Java CI with Gradle & GHCR Login

on:
  push:
    branches: [ "main", "master" ]
  pull_request:
    branches: [ "main", "master" ]

jobs:
  build-and-test:
    name: Build, Test and Login to GHCR
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Java JDK 17
        uses: actions/setup-java@v5
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'gradle'

      - name: Grant execute permission for gradlew
        run: chmod +x gradlew

      - name: Run unit tests with Gradle
        run: ./gradlew test

      - name: Build project with Gradle
        run: ./gradlew build -x test

      - name: Đăng nhập vào GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.REGISTRY_TOKEN }}
```

---

## 4. Hướng dẫn thiết lập GitHub Personal Access Token & Repository Secret (`REGISTRY_TOKEN`)

### Bước 1: Tạo Personal Access Token (Classic) với quyền `write:packages`
1. Đăng nhập vào GitHub -> Nhấn vào **ảnh đại diện (Avatar)** ở góc trên bên phải -> Chọn **Settings**.
2. Kéo xuống cuối thanh menu bên trái -> Chọn **Developer settings**.
3. Chọn **Personal access tokens** -> **Tokens (classic)**.
4. Nhấn **Generate new token** -> Chọn **Generate new token (classic)**.
5. Điền thông tin:
   - **Note**: `GHCR_REGISTRY_TOKEN` (hoặc tên gợi nhớ).
   - **Expiration**: `30 days` hoặc `90 days`.
   - **Select scopes**: Tích chọn **`write:packages`** *(Upload packages to GitHub Package Registry - hệ thống sẽ tự động tích kèm `read:packages` và `repo`)*.
6. Nhấn **Generate token** ở cuối trang và **Sao chép (Copy)** ngay mã token vừa sinh ra (`ghp_...`).

---

### Bước 2: Khai báo biến `REGISTRY_TOKEN` trong Repository Secrets
1. Truy cập vào **Repository** của bài tập trên GitHub.
2. Chọn tab **Settings** (trên thanh menu ngang của Repository).
3. Ở menu bên trái, mục **Security**, nhấn vào **Secrets and variables** -> Chọn **Actions**.
4. Tại tab **Secrets**, mục **Repository secrets**, nhấn nút **New repository secret** (màu xanh lá).
5. Nhập thông tin:
   - **Name**: `REGISTRY_TOKEN`
   - **Secret**: Dán mã Personal Access Token (`ghp_...`) vừa copy ở Bước 1.
6. Nhấn **Add secret**.
7. **Chụp ảnh màn hình** mục **Repository secrets** hiển thị biến `REGISTRY_TOKEN` đã khởi tạo và lưu vào thư mục `homework/session_11/ex4/` (ví dụ: `homework/session_11/ex4/registry-token-secret.png`) để nộp bài.

---

### Bước 3: Kiểm tra kết quả trên GitHub Actions (`Login Succeeded`)
1. Đẩy mã nguồn lên GitHub:
   ```bash
   git init
   git add .
   git commit -m "feat(ex4): configure GHCR login with REGISTRY_TOKEN secret"
   git branch -M main
   git remote add origin <URL_GITHUB_REPO_CUA_BAN>
   git push -u origin main
   ```
2. Mở tab **Actions** trên Repository GitHub -> Chọn workflow **Java CI with Gradle & GHCR Login** vừa chạy -> Mở job **Build, Test and Login to GHCR**.
3. Mở chi tiết bước **Đăng nhập vào GitHub Container Registry**, log sẽ hiển thị:
   ```text
   Run docker/login-action@v3
     with:
       registry: ghcr.io
       username: <ten-tai-khoan-github>
       password: ***
       ecr: auto
       logout: true
   Logging into ghcr.io...
   Login Succeeded!
   ```
