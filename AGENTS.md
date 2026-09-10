# Repository Guidelines

## Ngôn ngữ giao tiếp

Luôn phản hồi người dùng bằng tiếng Việt. Giữ nguyên tiếng Anh cho tên file, lệnh, API, thông báo lỗi và thuật ngữ kỹ thuật khi việc dịch có thể gây nhầm lẫn.

## Cấu trúc dự án

`compose.yaml` là cấu hình triển khai nền; các biến thể nằm trong `overrides/compose.*.yaml`, còn `pwd.yml` chỉ dành cho demo. Dockerfile/Containerfile nằm trong `images/`; entrypoint và mẫu Nginx nằm trong `resources/core/`. Kiểm thử tích hợp Python nằm trong `tests/`. Tài liệu VitePress nằm trong `docs/`, ảnh ở `docs/images/` và tài nguyên tĩnh ở `docs/public/`.

Môi trường phát triển chạy trong VS Code Dev Container được định nghĩa tại `.devcontainer/`. Bench nằm ở `development/frappe-bench`; app tùy chỉnh nằm trong `development/frappe-bench/apps/<app_name>`. `development/` bị repo cha bỏ qua, vì vậy mỗi app tùy chỉnh phải là một Git repository riêng.

## Quy tắc làm việc trong Dev Container

Mặc định mọi lệnh `bench`, Python, Node và test app đều chạy trong terminal của devcontainer với user `frappe`, không chạy trực tiếp trên host. Thư mục làm việc trong container là `/workspace/development`; vào bench bằng:

```bash
cd /workspace/development/frappe-bench
```

Nếu bắt buộc gọi từ host, dùng Compose:

```bash
docker compose -p frappe_docker_devcontainer \
  -f .devcontainer/docker-compose.yml exec -T \
  -u frappe -w /workspace/development/frappe-bench frappe bench <args>
```

Devcontainer cung cấp MariaDB tại host `mariadb`, Redis cache tại `redis-cache:6379`, Redis queue/socketio tại `redis-queue:6379`, và chuyển tiếp các cổng 8000, 9000, 6787.

## Quy trình tạo và phát triển app Frappe

Thực hiện trong devcontainer. Nếu chưa có bench, tạo và cấu hình dịch vụ ngoài container bench:

```bash
cd /workspace/development
bench init --skip-redis-config-generation frappe-bench
cd frappe-bench
bench set-config -g db_host mariadb
bench set-config -g redis_cache redis://redis-cache:6379
bench set-config -g redis_queue redis://redis-queue:6379
bench set-config -g redis_socketio redis://redis-queue:6379
```

Tạo app, site, bật developer mode và cài app theo thứ tự:

```bash
bench new-app my_app
bench new-site --db-root-password 123 --admin-password admin \
  --mariadb-user-host-login-scope=% my-app.localhost
bench --site my-app.localhost set-config developer_mode 1
bench --site my-app.localhost install-app my_app
bench --site my-app.localhost list-apps
bench start
```

Tên app/package dùng `snake_case`; tên site phát triển phải kết thúc bằng `.localhost`. Luôn ghi rõ `--site`; không dựa vào site mặc định. Với app có sẵn, dùng `bench get-app <git-url> --branch <branch>` trước `install-app`. Sau khi sửa schema hoặc hook, chạy `bench --site <site> migrate`; sau khi sửa JS/CSS, chạy `bench build --app <app>`; khi cần làm mới metadata, chạy `bench --site <site> clear-cache`. Trước khi commit app, chạy `bench --site <site> run-tests --app <app>`.

## Build, kiểm thử và định dạng repository

- `FRAPPE_VERSION=develop ERPNEXT_VERSION=develop docker buildx bake erpnext`: build image; xem thêm target trong `docker-bake.hcl`.
- `python3 -m venv venv && venv/bin/pip install -r requirements-test.txt && venv/bin/pytest`: chạy integration test của repository trên host có Docker.
- `pre-commit run --all-files`: chạy Black, isort, Prettier, codespell, shfmt, ShellCheck, Hadolint và kiểm tra Compose.
- `cd docs && corepack enable && pnpm install --frozen-lockfile && pnpm docs:dev`: chạy tài liệu cục bộ.

Tuân thủ `.editorconfig`: LF, UTF-8, hai khoảng trắng mặc định, bốn khoảng trắng cho Python và Dockerfile. File test là `test_*.py`; override mới phải theo mẫu `overrides/compose.<purpose>.yaml` và được thêm vào `tests/compose-configs.json`.

## Commit và Pull Request

Dùng Conventional Commits, ví dụ `feat(api): add consent endpoint` hoặc `fix(ci): validate compose overrides`. Branch dùng `feature/`, `fix/` hoặc `docs/`. PR phải giải thích vấn đề và giải pháp, liên kết issue liên quan, cập nhật tài liệu, chạy test/pre-commit phù hợp và kèm ảnh khi giao diện được chỉnh sửa.

Không commit secret, `site_config.json`, database, file upload hoặc credential registry. Dùng file môi trường cục bộ đã được Git bỏ qua; xem `example.env` chỉ như tài liệu cấu hình.
