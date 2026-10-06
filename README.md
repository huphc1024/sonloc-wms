# Sơn Lộc WMS

**Hệ thống quản lý kho (WMS) của công ty Sơn Lộc** — nhận hàng, cất hàng, soạn hàng, đóng gói, giao hàng, kiểm kê và chuyển kho, chạy trên máy quét mã vạch cầm tay và trang quản trị web.

Sơn Lộc WMS không phải ERP: hệ thống xử lý lớp thực thi kho vật lý và kết nối với ERP / hệ thống bán hàng hiện có qua API (inbound, polling, webhook, connector).

## Thành phần

| Thư mục | Vai trò | Công nghệ |
|---------|---------|-----------|
| `api/` | REST API, nghiệp vụ kho, tích hợp, Celery jobs | Python / Flask |
| `admin/` | Trang quản trị (cổng 8080) | React / Vite |
| `portal/` | Cổng khách hàng 3PL (cổng 8081) | React / Vite |
| `mobile/` | Ứng dụng máy quét Android (Chainway) | React Native / Expo |
| `db/` | Schema PostgreSQL, migrations, dữ liệu mẫu, mapping templates | PostgreSQL 16 |
| `proxy/` | Cấu hình nginx reverse proxy (TLS) | nginx |
| `scripts/` | Script cài đặt / khởi tạo môi trường local | PowerShell / Python |
| `tools/` | Script sinh lại OpenAPI / schema (dùng trong CI) và kịch bản load test k6 | Python / k6 |

## Chạy nhanh với Docker Compose

```bash
cp .env.example .env
# Điền các secret bắt buộc trong .env (xem chú thích trong .env.example):
#   JWT_SECRET, SENTRY_ENCRYPTION_KEY, REDIS_PASSWORD,
#   SENTRY_TOKEN_PEPPER, SENTRY_PUBSUB_HMAC_KEY
# API sẽ dừng khi khởi động nếu thiếu secret.

docker compose up -d
# Bỏ dữ liệu demo:      SKIP_SEED=true docker compose up -d
# Dev (Vite hot reload): docker compose -f docker-compose.yml -f docker-compose.dev.yml up
```

- API: http://localhost:5000 (health: `/api/health`)
- Trang quản trị: http://localhost:8080 — cài mới đăng nhập `admin` / `admin`, bắt buộc đổi mật khẩu lần đầu
- Cổng khách hàng: http://localhost:8081

Ứng dụng di động: `cd mobile && npm install`, đặt `EXPO_PUBLIC_API_URL` trong `mobile/.env`, rồi build APK nội bộ (xem `docs/local-setup-windows.md`).

## Tài liệu

Tài liệu nằm trong `docs/` (có thể dựng site bằng `mkdocs serve`):

- [Triển khai](docs/deployment.md) · [Cài đặt local trên Windows](docs/local-setup-windows.md) · [Go-live](docs/runbooks/go-live.md)
- [API reference](docs/api-reference.md) · [Customer API](docs/customer-api.md) · [Tích hợp ERP](docs/erp-integration.md) · [Webhooks](docs/api/webhooks.md)
- [Trang quản trị](docs/admin-panel.md) · [Phân quyền](docs/role-matrix.md) · [Kho 3D](docs/warehouse-3d.md)
- [Gợi ý AI](docs/ai-suggestions.md) · [Trợ lý AI](docs/ai-assistant.md) · [Dữ liệu demo giống production](docs/prodlike-data.md)
- Bảo mật: [SECURITY.md](SECURITY.md) · Lịch sử thay đổi: [CHANGELOG.md](CHANGELOG.md)

## Kiểm thử

```bash
cd api && python -m pytest tests_unit -q        # unit test API (không cần DB)
cd admin && npx vitest run && npm run build
cd portal && npx vitest run
cd mobile && npx vitest run
```

## Giấy phép

Phần mềm độc quyền của Sơn Lộc — xem [LICENSE-PROPRIETARY.md](LICENSE-PROPRIETARY.md).
Sơn Lộc WMS được phát triển từ Sentry WMS (Apache License 2.0); các phần gốc vẫn theo giấy phép đó — xem [LICENSE](LICENSE) và [NOTICE](NOTICE).
