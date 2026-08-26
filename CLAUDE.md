# frappe_docker — môi trường phát triển `web_tracking`

Repo này là bản clone của `frappe/frappe_docker` (`origin` trỏ upstream, branch `main`).
Custom app được phát triển bên trong bench ở `development/frappe-bench/`.

---

## 1. Kiến trúc môi trường

Bench chạy trong VS Code devcontainer, **không** chạy trên host.

| | |
|---|---|
| Container | `frappe_docker_devcontainer-frappe-1` |
| Bench (trong container) | `/workspace/development/frappe-bench` |
| Bench (trên host) | `./development/frappe-bench` |
| Bind mount | `..:/workspace:cached` |
| Frappe / Bench CLI | 16.31.0 / 5.31.0 |
| DB / Redis | service `mariadb`, `redis-cache:6379`, `redis-queue:6379` |

Sửa file **trực tiếp trên host**. Bind mount làm mọi thay đổi có hiệu lực ngay trong
container — không cần đồng bộ hay restart. Chỉ lệnh `bench` mới phải đi qua `docker exec`.

### Chạy lệnh bench

```bash
docker exec -u frappe -w /workspace/development/frappe-bench \
  frappe_docker_devcontainer-frappe-1 bench <args>
```

Dạng rút gọn khi chạy nhiều lệnh liên tiếp:

```bash
BENCH="docker exec -u frappe -w /workspace/development/frappe-bench frappe_docker_devcontainer-frappe-1 bench"
$BENCH --site tracking.localhost migrate
```

---

## 2. Sites — luôn ghi rõ `--site`

Chỉ còn **một** site (`development.localhost` đã bị xóa). Vẫn không có site mặc định,
mọi lệnh phải chỉ định `--site`.

| Site | Apps đã cài | `developer_mode` |
|---|---|---|
| `tracking.localhost` | frappe, web_tracking | 1 |

`developer_mode = 1` là bắt buộc: không có nó, DocType tạo qua giao diện desk chỉ nằm
trong database và không bao giờ được ghi ra file `.json` để commit.

---

## 3. App `web_tracking`

- Đường dẫn: `development/frappe-bench/apps/web_tracking`
- Repo: `https://github.com/marcus-GLAC/web-tracking-frappe` (private), branch `main`
- Module: `Web Tracking`
- Mục đích: đồng bộ consent / lead / session / event từ script tracking trên website,
  phục vụ báo cáo attribution marketing.

### Cấu trúc

| Loại | Tên |
|---|---|
| DocTypes | `Web Tracking Settings`, `Web Tracking Lead`, `Web Tracking Session`, `Web Tracking Event`, `Web Tracking Consent Log` |
| Reports | `web_tracking_kpi_summary`, `web_tracking_pivot_by_lead`, `web_tracking_raw_data`, `web_tracking_top_leads_by_sessions` |
| Number cards | sessions, leads_with_consent, consent_denied, cta_clicks, left_contact |
| Role | `Web Tracking Marketing Viewer` |
| Workspace | `web_tracking` |

### Điểm vào

- **Scheduler**: cron `*/10 * * * *` → `web_tracking.tasks.sync.sync_all`
- **API whitelisted**: `web_tracking.api.export_raw_data_xml` (chỉ `GET`)
- **Permission app screen**: `web_tracking.api.has_app_permission`

### Ràng buộc ngôn ngữ

`pyproject.toml` đặt `requires-python = ">=3.14"`, ruff `line-length = 110`,
`target-version = "py314"`. Viết code theo đúng các giá trị này, đừng đoán.

---

## 4. Vòng lặp phát triển

| Vừa sửa gì | Chạy lệnh gì |
|---|---|
| DocType `.json`, `hooks.py`, patch | `$BENCH --site tracking.localhost migrate` |
| JS / CSS trong `public/` | `$BENCH build --app web_tracking` |
| Python thuần | `$BENCH --site tracking.localhost clear-cache` |
| Bất cứ gì, trước khi commit | `$BENCH --site tracking.localhost run-tests --app web_tracking` |

Sửa DocType bằng cách **sửa file `.json` trong app**, không sửa qua UI rồi quên export.

---

## 5. Bẫy đã gặp trong dự án này

### 5.1 App không hiện trên màn hình app switcher

Khai báo `add_to_apps_screen` trong `hooks.py` **không đủ**. Màn hình đó do doctype
`Desktop Icon` điều khiển — dữ liệu nằm trong DB, sinh một lần lúc cài app. App thêm hook
*sau* thời điểm đó sẽ không bao giờ có row, và clear-cache hay hard-refresh đều vô ích.

Quy trình đúng:

1. Khai báo `add_to_apps_screen` trong `hooks.py`.
2. Thêm logo vào `public/images/`, chạy `$BENCH build --app web_tracking`.
3. `$BENCH --site <site> execute frappe.desk.doctype.desktop_icon.desktop_icon.create_desktop_icons_from_installed_apps`
4. **Kiểm tra trùng lặp ngay**: hàm này duyệt *tất cả* app đã cài và so khớp
   `label == app_title`. Nếu row cũ của một app khác có label lệch với `app_title` hiện tại
   của nó, hàm sẽ tạo icon trùng cho app đó. Query
   ``select name, label, app, link from `tabDesktop Icon` where icon_type='App'``
   trước và sau, xóa row thừa nếu có.
5. `$BENCH --site <site> clear-cache`

Đây là lỗi dữ liệu, không phải lỗi cache. Đừng chẩn đoán theo hướng cache trình duyệt
hay port-forwarding của devcontainer.

### 5.2 Production dùng image bất biến

Trên production **không** chạy `bench get-app`, **không** chạy `bench build`. App và assets
nằm trong image; `sites/assets` được chuyển ra tầng image lúc build và entrypoint tạo symlink
trở lại. Chạy `bench build` lúc runtime làm `assets.json` lệch với assets thật → vỡ giao diện,
và restart không sửa được, phải recreate container.

Thêm hoặc cập nhật app = **build lại image + redeploy**. Xem mục 7.

### 5.3 Cấu hình nằm trong DB không tự đi theo git

`role/`, `workspace/`, `report/`, `number_card/` là file nên đã theo git. Nhưng Custom Field
và Property Setter tạo qua UI thì **không**: chúng nằm trong DB. Nếu app có dùng, phải khai
báo `fixtures` trong `hooks.py` rồi `$BENCH --site <site> export-fixtures`, nếu không chúng
sẽ biến mất khi lên production. Hiện `hooks.py` chưa có key `fixtures` — kiểm tra trước khi
build image lần đầu.

---

## 6. Git workflow

App `web_tracking` là repo riêng, lồng trong `development/` (thư mục này bị `frappe_docker`
gitignore, nên hai repo hoàn toàn độc lập).

```
main                    ← branch tích hợp, luôn ở trạng thái chạy được
  └── feat/<mô-tả>      ← branch tính năng
  └── fix/<mô-tả>       ← branch sửa lỗi
tag v0.1.0, v0.2.0 …    ← mốc dùng để ghim vào apps.json khi build image
```

Quy ước commit: Conventional Commits — `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`.

```bash
cd development/frappe-bench/apps/web_tracking

git switch -c feat/consent-export
# … sửa code trên host, chạy migrate/test qua $BENCH …
git add -A && git commit -m "feat: export consent log to xml"
git push -u origin feat/consent-export
gh pr create --fill

# sau khi merge vào main, gắn mốc để build image
git switch main && git pull
git tag v0.2.0 && git push origin v0.2.0
```

**Không** commit `__pycache__/`, `*.pyc` (đã có trong `.gitignore`) và không commit
`sites/` hay `site_config.json` — chúng chứa mật khẩu DB và encryption key.

---

## 7. Đường lên production

Nguồn sự thật là file `apps.json` ở gốc repo này (chưa tạo). Nó được truyền vào
`docker build` dưới dạng **BuildKit secret**, không phải build arg — build arg lộ token
vĩnh viễn trong `docker image history`.

```jsonc
// apps.json
[
  { "url": "https://github.com/marcus-GLAC/web-tracking-frappe", "branch": "v0.1.0" },
  { "url": "https://github.com/frappe/wiki", "branch": "master" }
]
```

```bash
docker build \
  --build-arg=FRAPPE_PATH=https://github.com/frappe/frappe \
  --build-arg=FRAPPE_BRANCH=version-16 \
  --build-arg=CACHE_BUST="$(sha256sum apps.json | awk '{print $1}')" \
  --secret=id=apps_json,src=apps.json \
  --tag=ghcr.io/marcus-glac/tracking:0.1.0 \
  --file=images/layered/Containerfile .
```

`CACHE_BUST` là bắt buộc: nội dung secret không tham gia cache key của Docker, nên nếu
thiếu nó, sửa `apps.json` xong Docker vẫn dùng lại layer cũ và build ra image không đổi.

Tên image phải **viết thường toàn bộ** — Docker từ chối reference có chữ hoa trước cả khi
gửi lên registry. Dùng `ghcr.io/marcus-glac/…`, không phải `marcus-GLAC`.

Deploy: `custom.env` đặt `CUSTOM_IMAGE` / `CUSTOM_TAG` / `PULL_POLICY`, rồi
`docker compose --env-file custom.env -f compose.yaml -f overrides/… config > compose.custom.yaml`.
Thêm `overrides/compose.migrator.yaml` để `bench --site all migrate` tự chạy mỗi lần stack khởi động.

Chi tiết đầy đủ trong `docs/02-setup/02-build-setup.md` và
`docs/03-production/06-automated-builds-and-deployment.md`.

### Đừng nhầm ba file `apps.json`

| File | Ai đọc | Vai trò |
|---|---|---|
| `development/apps-example.json` | `installer.py` | Dựng bench dev lần đầu |
| `apps.json` (gốc repo) | `docker build`, dạng secret | **Quyết định app nào có trong image production** |
| `development/frappe-bench/sites/apps.json` | bench, tự sinh | Metadata phiên bản đã cài. Không sửa tay |

---

## 8. Skills đã cài

Năm skill trong `.claude/skills/`, ghim từ repo `frappe/skills` qua `skills-lock.json`.
Chúng là bản pin — **không sửa tại chỗ**; muốn nâng cấp thì cập nhật từ nguồn.

| Skill | Nạp khi nào |
|---|---|
| **`frappe-app-dev`** | Mọi việc dựng app Frappe: tạo/sửa DocType, controller và lifecycle hook, whitelisted API, dựng app hoặc site mới, desk form và list view, portal page, background job và scheduled task, permission và role, viết test, dùng `frappe.db` / `frappe.qb`. Kích hoạt cả khi câu hỏi không nhắc chữ "Frappe" — ví dụ "hook vào lúc save", "thêm field", "chạy bench migrate". |
| **`code-style`** | Luôn nạp khi viết hoặc sửa code, và khi bàn về refactor, kích thước hàm/file, cấu trúc OO, thứ tự helper, comment. Việc thuộc Frappe thì ưu tiên `frappe-app-dev`. |
| **`quality-code-review`** | Review diff, PR hoặc một đoạn code của app Frappe — checklist về correctness, security, performance, concurrency, readability, API design, testing. |
| **`technical-writing`** | Viết văn xuôi theo "Simplified Technical English": tài liệu, README, commit message, mô tả PR, release note. Không áp dụng cho code và identifier. |
| **`ui-design`** | Phán đoán UI/UX chung về layout, hierarchy, spacing, typography, màu, accessibility — khi không có design system riêng của sản phẩm. |

`frappe-app-dev` có 18 file reference tải theo nhu cầu, không nạp hết cùng lúc:

```
api  background-jobs  bench-operations  caching  controllers  database  doctypes
existing-app  frontend  frontend-desk  frontend-portal  frontend-vue  hooks
new-app  permissions  realtime  site-management  testing
```
