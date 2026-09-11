# Kiểm kê chức năng Frappe Wiki

## Phạm vi kiểm tra

- App: `wiki 3.1.0`
- Branch: `main`
- Commit: `5bba767`
- Site: `wiki.localhost`
- Frappe: `16.33.1`
- Trạng thái worktree khi kiểm tra: sạch

## Chức năng đã có

| # | Nhóm | Chức năng hiện có | Cách xem hoặc kiểm tra | Trạng thái |
|---:|---|---|---|---|
| 1 | Wiki Space | Xem danh sách, tìm kiếm và lọc Space theo trạng thái xuất bản | Mở `/wiki-app`. Chọn `All Spaces`, dùng ô tìm kiếm và bộ lọc published. | Hoàn chỉnh |
| 2 | Wiki Space | Tạo Space thường hoặc Space đồng bộ từ GitHub | Trong `All Spaces`, bấm `New Space`. Form có tùy chọn `Synced from GitHub?`. | Hoàn chỉnh |
| 3 | Wiki Space | Đổi tên, đổi route prefix và cập nhật route của toàn bộ trang | Mở một Space. Vào `Space settings > General`, sửa tên hoặc bấm nút đổi `Route Prefix`. | Hoàn chỉnh |
| 4 | Wiki Space | Clone toàn bộ Space bằng background job | Mở `Space settings > General`. Bấm `Clone`, nhập route mới và xác nhận. | Hoàn chỉnh |
| 5 | Nhận diện | Logo, favicon, Lucide icon, màu và avatar tự sinh | Mở `Space settings > General > Space Logo` để chọn icon, màu hoặc avatar. Favicon và logo cũng có trong DocType `Wiki Space`. | Hoàn chỉnh |
| 6 | Điều hướng | Hiện hoặc ẩn Space trong switcher, sắp xếp và ghim Space lên đầu | Vào `Space settings > Navigation` để chỉnh switcher. Nhấp phải Space trong sidebar để ghim hoặc bỏ ghim. | Hoàn chỉnh |
| 7 | Thống kê | Hiện số trang, số Change Request đang review và lần sửa gần nhất | Mở `All Spaces`. Mỗi dòng hiển thị số trang, số Change Request và `Last Updated`. | Cơ bản |
| 8 | Phân quyền | Phân quyền riêng cho từng Space theo Role | Mở `Space settings > Access`. Tìm Role, thêm Role và chọn mức quyền. | Hoàn chỉnh |
| 9 | Phân quyền | Ba mức sử dụng: `Read`, `Write` và truy cập công khai bằng Role `Guest` | Trong `Space settings > Access`, chọn `Read` hoặc `Write`. Thêm Role `Guest` để thử truy cập ẩn danh. | Hoàn chỉnh |
| 10 | Đóng góp | Bật hoặc tắt `Accept Contributions` theo từng Space | Mở `Space settings > Access`. Bật hoặc tắt công tắc `Accept Contributions`. | Hoàn chỉnh |
| 11 | Cây tài liệu | Tạo trang, group và external link | Mở một Space. Dùng menu trên cây tài liệu và chọn `New Page`, `New Group` hoặc `External Link`. | Hoàn chỉnh |
| 12 | Cây tài liệu | Đổi tên, đổi URL, xóa, di chuyển và kéo thả sắp xếp | Mở menu của một node để đổi tên hoặc xóa. Kéo node sang vị trí khác để kiểm tra sắp xếp. | Hoàn chỉnh |
| 13 | Trang | Quản lý title, slug, route và trạng thái published | Mở một trang trong editor. Bấm `Page settings` và kiểm tra phần `General`. | Hoàn chỉnh |
| 14 | SEO trang | Meta title, meta description, meta image và social preview | Mở `Page settings > Social preview`. Thay meta fields và xem preview bên dưới. | Hoàn chỉnh |
| 15 | Editor | TipTap rich-text editor lưu nội dung dưới dạng Markdown | Mở một trang trong `/wiki-app`, nhập nội dung và lưu draft. Mở DocType `Wiki Document` để xem field `content`. | Hoàn chỉnh |
| 16 | Định dạng | Heading H1–H6, bold, italic, strike, inline code, quote và code block | Chọn văn bản trong editor và dùng toolbar hoặc bubble menu để áp dụng từng định dạng. | Hoàn chỉnh |
| 17 | Danh sách | Bullet list, numbered list và task list | Dùng toolbar hoặc gõ `/`. Chọn `Bullet List`, `Numbered List` hoặc `Task List`. | Hoàn chỉnh |
| 18 | Thành phần | Table, horizontal rule, link và slash command `/` | Gõ `/` trong editor và chọn `Table` hoặc `Horizontal Rule`. Dùng toolbar để chèn link. | Hoàn chỉnh |
| 19 | Media | Upload ảnh, video, PDF và xem PDF trực tiếp | Dùng toolbar hoặc slash menu để chèn ảnh, video hoặc PDF. Mở preview để kiểm tra trình xem PDF. | Hoàn chỉnh |
| 20 | Embed | YouTube, Vimeo, Loom, Figma và một số dịch vụ video được allowlist | Gõ `/embed` hoặc dán riêng một URL được hỗ trợ. Editor sẽ hiện iframe preview. | Hoàn chỉnh |
| 21 | Nội dung nâng cao | Mermaid diagram và callout `Note`, `Tip`, `Warning`, `Danger` | Gõ `/diagram` để chèn Mermaid. Gõ `/note`, `/tip`, `/warning` hoặc `/danger` để chèn callout. | Hoàn chỉnh |
| 22 | Editor TOC | Tạo mục lục H2/H3 theo thời gian thực và nhảy tới heading | Thêm các heading H2/H3 vào trang. Xem mục lục bên phải hoặc dải TOC trên màn hình hẹp. | Hoàn chỉnh |
| 23 | Local-first | Autosave, lưu bản nháp trong IndexedDB và phục hồi sau khi refresh hoặc crash | Sửa nội dung rồi tải lại trang trước khi gửi review. Draft cục bộ sẽ được phục hồi từ IndexedDB. | Hoàn chỉnh |
| 24 | Đồng thời | Phát hiện `operation_version` conflict và cho tải lại dữ liệu mới nhất | Mở cùng một draft trong hai tab và sửa cả hai. Tab cũ sẽ hiện cảnh báo conflict và `Reload latest`. | Hoàn chỉnh |
| 25 | Change Request | Gom thao tác tạo, sửa, xóa, di chuyển và sắp xếp trang vào bản nháp | Sửa cây hoặc nội dung trong một Space. Banner draft và change badges sẽ ghi nhận các thay đổi. | Hoàn chỉnh |
| 26 | Review | `Draft → In Review → Approved/Changes Requested/Rejected/Merged/Archived` | Mở `Change Requests`. Gửi một draft để review và theo dõi badge trạng thái sau mỗi hành động. | Hoàn chỉnh |
| 27 | Review | Assign reviewer, danh sách “My”, “Assigned to me” và “All in review” | Mở `Change Requests`. Chuyển giữa các tab và bấm `Assign` trên một yêu cầu đang review. | Hoàn chỉnh |
| 28 | Review | Xem diff hoặc preview trước khi duyệt | Mở một Change Request đang review. Chọn `Diff` hoặc `Preview` trên từng thay đổi. | Hoàn chỉnh |
| 29 | Review | Approve, Approve & Merge, Request Changes, Reject và Withdraw | Mở Change Request bằng tài khoản có quyền phù hợp. Các nút xuất hiện theo Role, owner và trạng thái. | Hoàn chỉnh |
| 30 | Merge | Three-way merge và xử lý conflict bằng `Keep Main` hoặc `Keep Your Changes` | Tạo hai Change Request sửa cùng trang. Merge yêu cầu đầu, sau đó merge yêu cầu còn lại để mở conflict panel. | Hoàn chỉnh |
| 31 | Thông báo | Realtime toast và `Notification Log` khi reviewer thay đổi trạng thái | Reviewer duyệt hoặc yêu cầu sửa. Tài khoản tác giả sẽ nhận toast và Notification Log. | Hoàn chỉnh |
| 32 | Phiên bản | Lưu revision, content blob và snapshot phục vụ Change Request | Mở Desk và xem `Wiki Revision`, `Wiki Revision Item` và `Wiki Content Blob` sau khi tạo Change Request. | Backend có sẵn |
| 33 | Public reader | Sidebar, breadcrumb, previous/next, mục lục và heading anchor | Mở một trang published như `/bonus/tesst`. Kiểm tra sidebar, breadcrumb, TOC và nút previous/next. | Hoàn chỉnh |
| 34 | Public reader | Điều hướng SPA, prefetch trang và hiển thị thời gian cập nhật | Nhấp nhiều trang trong public sidebar. URL và nội dung đổi mà trình duyệt không tải lại toàn trang. | Hoàn chỉnh |
| 35 | Giao diện | Dark/light theme và giao diện responsive cho mobile/tablet | Bấm nút theme trên public reader. Thu nhỏ cửa sổ để xem mobile header và navigation drawer. | Hoàn chỉnh |
| 36 | Tìm kiếm | Full-text search bằng SQLite, lọc theo Space và kiểm tra quyền truy cập | Trên public reader, bấm Search hoặc nhấn `Ctrl/Command + K`. Nhập từ khóa có trong một trang published. | Hoàn chỉnh |
| 37 | Feedback | Đánh giá `Good`, `Ok`, `Bad`, thêm nhận xét và giới hạn số lần gửi | Bật `Collect Feedback` trong `Space settings > Access`. Mở public page và xem widget cuối trang. | Theo từng Space |
| 38 | Hành động trang | Download PDF và copy nội dung Markdown | Trên public page, bấm mũi tên cạnh `Edit` hoặc `Copy`. Chọn `Download` hoặc `Copy page`. | Hoàn chỉnh |
| 39 | AI | Mở trang hiện tại trong ChatGPT hoặc Claude bằng prompt chứa URL | Mở `/bonus/tesst`, bấm mũi tên cạnh `Edit`, rồi chọn `Open in ChatGPT` hoặc `Open in Claude`. | Hoàn chỉnh |
| 40 | Hình ảnh | Lightbox cho ảnh và tự chuyển PNG/JPEG sang WebP | Nhấp ảnh trên public page để mở lightbox. Bật Auto WebP, upload PNG/JPEG và kiểm tra URL file `.webp`. | Hoàn chỉnh |
| 41 | Meta image | Tự sinh OG image 1200×630 khi trang không có meta image riêng | Bật `Auto Generate Meta Images`. Mở `Page settings > Social preview` khi trang chưa có meta image riêng. | Hoàn chỉnh |
| 42 | Crawler/LLM | `/<route>.md` và phản hồi Markdown qua `Accept: text/markdown` | Thêm `.md` vào URL trang, ví dụ `/bonus/tesst.md`. Có thể gửi header `Accept: text/markdown` tới URL gốc. | Hoàn chỉnh |
| 43 | Crawler/LLM | `/llms.txt` toàn site và `/<space>/llms.txt` | Mở `/llms.txt` hoặc `/bonus/llms.txt` trên site có Space công khai. | Hoàn chỉnh |
| 44 | SEO | `sitemap.xml`, canonical URL và JSON-LD breadcrumb | Mở `/sitemap.xml`. Xem source của public page để kiểm tra canonical URL và `BreadcrumbList`. | Hoàn chỉnh |
| 45 | GitHub sync | GitHub App, OAuth, private repository, chọn branch và thư mục docs | Mở `Wiki Settings > GitHub Sync` để cấu hình App. Tạo Space mới để xem bộ chọn repository, branch và docs folder. | Có trong code |
| 46 | GitHub sync | One-way sync `GitHub → Wiki`, Space trở thành read-only | Tạo Git-synced Space và chạy sync. Mở trang trong editor để thấy `Read-only` và `Edit on GitHub`. | Có trong code |
| 47 | GitHub sync | Đọc Markdown/MDX, YAML front matter và `.wiki.json` | Thêm các file này vào docs folder của repository. Bấm `Sync now` và kiểm tra cây trang cùng cấu hình đã đọc. | Có trong code |
| 48 | GitHub sync | Import ảnh, sửa internal link và hỗ trợ README/index làm landing page | Sync repository có ảnh tương đối, internal link và README/index. Kiểm tra file ảnh, route và landing page sau sync. | Có trong code |
| 49 | GitHub sync | Sync thủ công, webhook push, realtime status và lịch sử sync | Mở `Space settings > Git Sync`. Dùng `Sync now`, webhook URL và danh sách các lần sync. | Có trong code |
| 50 | Global settings | TOC, WebP, generated meta image, feedback limit, `<head> HTML` và GitHub credentials | Trong sidebar, mở `Settings`. Kiểm tra các tab `General`, `Feedback`, `Header & Robots` và `GitHub Sync`. | Có giao diện |

## File chính đã đối chiếu

- [router.js](../../development/frappe-bench/apps/wiki/frontend/src/router.js)
- [WikiEditor.vue](../../development/frappe-bench/apps/wiki/frontend/src/components/WikiEditor.vue)
- [wiki_change_request.py](../../development/frappe-bench/apps/wiki/wiki/frappe_wiki/doctype/wiki_change_request/wiki_change_request.py)
- [permissions.py](../../development/frappe-bench/apps/wiki/wiki/permissions.py)
- [wiki_space.py](../../development/frappe-bench/apps/wiki/wiki/wiki/doctype/wiki_space/wiki_space.py)
- [git_sync.py](../../development/frappe-bench/apps/wiki/wiki/wiki/git_sync.py)
- [wiki_document.py](../../development/frappe-bench/apps/wiki/wiki/frappe_wiki/doctype/wiki_document/wiki_document.py)

## Trạng thái trên `wiki.localhost`

| Hạng mục | Hiện tại |
|---|---|
| Wiki Spaces | 2: `Wiki` tại `/docs` và `Bonus` tại `/bonus` |
| Xuất bản | Cả hai Space đã published |
| Contributions | Đang bật cho cả hai Space |
| Feedback theo Space | Đang bật cho cả hai Space |
| Wiki Documents | 5 bản ghi, gồm 2 trang đang published |
| Change Requests | 4 bản ghi: 3 `Merged`, 1 `Draft` |
| Feedback đã nhận | 0 |
| Table of Contents | Bật |
| Auto WebP | Bật |
| Auto-generated meta image | Bật |
| GitHub App | Chưa cấu hình |
| Git-synced Space | Chưa có |

## Phần chưa hoạt động hoặc còn thiếu

| Chức năng | Tình trạng |
|---|---|
| Wiki-wide analytics | Trang Overview mới có danh sách và số liệu cơ bản. Analytics chi tiết chưa làm |
| Horizontal tab navigation | Giao diện v3 đã bỏ chức năng này. Các field `enable_tabs`, `is_tab`, `tab_icon` chỉ hỗ trợ dữ liệu cũ |
| Revision history UI | Backend lưu revision nhưng chưa có giao diện để người dùng duyệt lịch sử |
| Xóa Wiki Space | Backend xử lý cleanup, nhưng SPA chưa có nút xóa Space |
| Global `Enable Feedback` | Settings có field này, nhưng public widget dùng `enable_feedback_collection` của từng Space |
| `Wiki Settings.javascript` | Field còn trong schema nhưng renderer hiện tại không dùng field này |
| `default_wiki_space` và `ask_for_contact_details` | Các field cũ chưa nối với luồng SPA hiện tại |
| GitHub sync trên site | Code đã có nhưng chưa dùng được vì GitHub App chưa cấu hình |
