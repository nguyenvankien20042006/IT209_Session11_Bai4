# Hướng Dẫn Nộp Bài 4 (`homework/session_11/ex4/`)

## 1. Sản phẩm đã hoàn thiện trong thư mục này
- Tệp kịch bản [`.github/workflows/ci.yml`](./.github/workflows/ci.yml) đã tích hợp bước đăng nhập GitHub Container Registry (`docker/login-action@v3`) sử dụng biến `secrets.REGISTRY_TOKEN`.

## 2. Bước đăng nhập GHCR trong `ci.yml`

```yaml
      - name: Đăng nhập vào GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.REGISTRY_TOKEN }}
```

## 3. Hướng dẫn chụp ảnh màn hình `REGISTRY_TOKEN` để hoàn tất nộp bài
1. Tạo **Personal Access Token (classic)** tại: `GitHub Settings` -> `Developer settings` -> `Personal access tokens` -> `Tokens (classic)` -> Tích quyền **`write:packages`**.
2. Vào Repository trên GitHub -> `Settings` -> `Secrets and variables` -> `Actions` -> Nhấn **New repository secret**:
   - **Name**: `REGISTRY_TOKEN`
   - **Secret**: Dán mã token `ghp_...` vừa tạo.
3. Chụp ảnh màn hình giao diện **Actions secrets and variables** hiển thị dòng **`REGISTRY_TOKEN`** trong mục **Repository secrets** và lưu ảnh vào thư mục `homework/session_11/ex4/` này.
4. Push code lên GitHub và kiểm tra tab **Actions** -> Bước **Đăng nhập vào GitHub Container Registry** hiển thị dòng chữ **`Login Succeeded!`**.
