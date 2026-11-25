# Nhận xét hợp đồng OpenAPI — catalog-service (TH2-1)

## 1. Bảng thao tác
| Thao tác | Thành công | Lỗi đã khai báo | An toàn? | Idempotent? |
|---|---|---|---|---|
| GET /api/v1/books | 200 | 400 | có | có |
| POST /api/v1/books | 201 + Location + ETag | 400, 409 | không | không |
| GET /api/v1/books/{isbn} | 200 + ETag, hoặc 304 | 404 | có | có |
| PUT /api/v1/books/{isbn} | 200 + ETag mới | 400, 404, 412, 428 | không | có |
| DELETE /api/v1/books/{isbn} | 204 | 404 | không | có |

## 2. Mã trạng thái theo RFC 9110
Tạo mới trả 201 kèm Location; xoá trả 204; GET có điều kiện trả 304; PUT lệch phiên bản trả 412; PUT thiếu If-Match trả 428. Hợp đồng khai báo If-Match là `required: false` để máy chủ tự trả 428 theo Problem Details thay vì 400 chung chung.

## 3. Đối chiếu ràng buộc với XSD ở TH1
Isbn13 `97[89][0-9]{10}` khớp; Money: minimum 0 + multipleOf 0.01 tương đương minInclusive 0 + fractionDigits 2; currency VND/USD.

## 4. An toàn / idempotent và vì sao PUT cần If-Match
GET an toàn và idempotent; PUT/DELETE idempotent; POST không. If-Match chống mất cập nhật (lost update): người ghi sau với ETag cũ nhận 412.

## 5. Thêm trường bắt buộc vào BookCreateRequest
Phá vỡ tương thích ngược (client cũ bị 400); nên thêm trường tùy chọn hoặc phát hành /api/v2.
