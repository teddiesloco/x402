# Goal

Tài liệu này tóm tắt **intent** và **goal** của repo x402 (fork `teddiesloco/x402` của `x402-foundation/x402`), nhìn từ ba góc: **BA** (Business Analyst), **PO** (Product Owner) và **PM** (Product Manager). Nguồn: `README.md`, `specs/`, `CONTRIBUTING.md`, `PROJECT-IDEAS.md`, `TSC.md`, `.agents/skills/`.

Khi goal thay đổi, sửa file này trước, rồi cập nhật phần "Memory" trong `CLAUDE.md`.

## Intent (vì sao dự án tồn tại)

x402 biến mã HTTP `402 Payment Required` thành một **chuẩn mở cho thanh toán gốc trên internet**: một client (người hoặc AI agent) gọi một resource, nhận yêu cầu thanh toán, ký payload, gửi lại và nhận kết quả, tất cả trong luồng HTTP thông thường. Không cần tài khoản, API key hay checkout riêng.

Người hưởng lợi chính là **AI agent và dịch vụ trả tiền theo lượt gọi** (pay-per-call API, MCP tool, dữ liệu, inference).

## Goal (dự án muốn đạt được gì)

1. **Chuẩn mở, không phụ thuộc một bên**: ai cũng dùng được, không bắt buộc facilitator nào. Quản trị bởi x402 Foundation và TSC (Coinbase, Cloudflare, Stripe).
2. **HTTP/transport native**: không thêm request ngoài luồng client/server thông thường. Có spec cho HTTP, MCP và A2A (`specs/transports-v2/`).
3. **Không phụ thuộc network, token, tiền tệ**: hỗ trợ EVM, Solana, Aptos, Stellar, Hedera, Sui, TON, XRPL, Cardano, Casper, NEAR… và mở đường cho fiat, nhưng không bao giờ ưu tiên fiat hơn onchain.
4. **Tích hợp tối thiểu**: 1 dòng cho server (middleware), 1 hàm cho client (fetch/axios wrapper).
5. **Trust minimizing**: facilitator và resource server không thể di chuyển tiền ngoài ý muốn của client.
6. **Tương thích ngược**: không bỏ network đang hỗ trợ trừ khi vì bảo mật; v1 được đóng băng (chỉ vá bảo mật).
7. **Mở rộng được**: thêm scheme (`exact`, `upto`, `batch-settlement`, `auth-capture`) và extension (Bazaar, Sign-in-with-X, gas sponsoring…) mà không đổi lõi.

## Góc nhìn BA (Business Analyst): bài toán và yêu cầu

**Bài toán.** Thanh toán trên web hiện nay cần tài khoản, thẻ, subscription, API key. Điều đó chặn micropayment và chặn AI agent tự trả tiền cho dịch vụ.

**Actor.**

| Actor | Nhu cầu |
|---|---|
| Client (người, agent) | Trả đúng số tiền cho đúng resource, không phải lo gas/RPC |
| Resource server (seller) | Thu tiền với ít code nhất, được đảm bảo đã thanh toán |
| Facilitator | Verify và settle payload cho một hoặc nhiều network |

**Luồng nghiệp vụ chính** (chi tiết: `README.md` mục "Typical x402 flow", `specs/x402-specification-v2.md`):
request → `402` + header `PAYMENT-REQUIRED` → client ký `PaymentPayload` → gửi lại với `PAYMENT-SIGNATURE` → verify → phục vụ → settle → `200` + `PAYMENT-RESPONSE`.

**Yêu cầu phi chức năng.** Đúng tuyệt đối với logic ký và settle (tiền thật), CAIP-2 cho network, đơn vị atomic cho số tiền, spec không phụ thuộc transport.

## Góc nhìn PO (Product Owner): sản phẩm và backlog

**Sản phẩm giao cho người dùng.**

- **Specs** (`specs/`): nguồn sự thật của giao thức. Code phải theo spec, không được đoán.
- **SDK tham chiếu**: TypeScript (`typescript/`, SDK chính), Python (`python/x402`), Go (`go/`). Java và mọi thư mục `legacy/` đã đóng băng.
- **Middleware/client HTTP**: express, hono, next, fastify, fetch, axios, paywall; MCP (`@x402/mcp`).
- **Docs** (`docs/`, Mintlify, docs.x402.org) và **examples** (`examples/`), **e2e** (`e2e/`).

**Tiêu chí chấp nhận cho mọi thay đổi** (từ `CONTRIBUTING.md` và `.agents/skills/contributing`):

- Một vấn đề mỗi PR, diff nhỏ nhất có thể.
- Bug fix phải có test fail trước và pass sau.
- Format, lint, build, test pass cho SDK bị ảnh hưởng.
- Có changelog fragment nếu thay đổi chạm tới người dùng.
- Commit được ký (bắt buộc khi gửi upstream).
- Đổi code SDK thì kiểm tra docs liên quan theo bảng mapping trong `docs/AGENTS.md`.

**Thứ tự ưu tiên backlog (đề xuất).** Bảo mật và đúng đắn thanh toán → tương thích spec giữa ba SDK → trải nghiệm tích hợp (1 dòng / 1 hàm) → network và scheme mới → extension.

## Góc nhìn PM (Product Manager): chiến lược và đo lường

**Định vị.** Lớp thanh toán mặc định cho internet và cho kinh tế agent, trung lập về chain và nhà cung cấp.

**Chiến lược.**

- Mở rộng hệ sinh thái qua network/scheme/extension do cộng đồng đóng góp, giữ tiêu chí chấp nhận chặt.
- Giảm ma sát cho seller: public facilitator cho testnet; production thì chọn rõ facilitator provider, tự chạy, hoặc self-facilitate.
- Kích cầu bằng use case agent (`PROJECT-IDEAS.md`: trading bot, oracle, KYC check, commerce agent, MCP registry shopper…) và micro-grant.

**Chỉ số theo dõi (đề xuất, repo chưa định nghĩa).**

- Số network và `(scheme, network)` được hỗ trợ đồng đều ở cả ba SDK.
- Lượt tải package `@x402/*` và `x402` (PyPI), số dịch vụ trên các directory (x402scan, Bazaar).
- Tỷ lệ e2e pass trên mọi SDK và network; thời gian từ issue đến fix cho lỗi thanh toán.

**Rủi ro.** Lỗi ký/settle gây mất tiền; lệch hành vi giữa các SDK; phụ thuộc vào public facilitator cho mainnet; PR do AI sinh ra không được review (xem chính sách AI trong `CONTRIBUTING.md`).

## Goal của fork này

Chưa được ghi lại. Fork hiện trùng với upstream (chưa có commit riêng). Khi Teddy xác định mục tiêu riêng (ví dụ: xây dịch vụ trả phí trên x402, đóng góp upstream, thử nghiệm scheme mới), ghi vào đây.

- [ ] Mục tiêu riêng của fork: _chưa xác định_
