# Recovery-Compiler 🤖

GitHub Actions workflow для сборки TWRP Recovery для Android-устройств.
Работает как вручную (через вкладку **Actions**), так и как Composite Action
из другого workflow.

## Возможности 📜

- Полная сборка TWRP без локальной настройки окружения.
- Поддержка Android 11 / 12.1 / 14.1 (манифесты `twrp-11.0`, `twrp-12.1`, `twrp-14.1`).
- Автоматическая сборка в `vendor_boot.img` для устройств без отдельного раздела `recovery` (A/B, GKI, Unisoc UMS9230 и др.).
- Публикация результата в **GitHub Releases** (постоянные ссылки, не артефакты на 90 дней).
- Встроенный swap 24 ГБ и очистка диска — сборка не падает по OOM.

## Требования ✋

- Runner: `ubuntu-22.04` (в workflow прописан явно, `ubuntu-latest` тоже подойдёт, но может измениться в будущем).
- Дерево устройства должно поддерживать формат `lunch twrp_<codename>-ap2a-eng` для Android 14+ или `twrp_<codename>-eng` для более старых веток.

## Использование через Actions (рекомендуется) 👨‍💻

1. Форкните репозиторий или создайте свой с `.github/workflows/twrp.yml` из этого репо.
2. Перейдите в **Actions** → выберите workflow **Recovery Build** → **Run workflow**.
3. Заполните параметры:

| Параметр | Описание | Пример |
|---|---|---|
| `MANIFEST_URL` | URL манифеста TWRP | `https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp` |
| `MANIFEST_BRANCH` | Ветка манифеста | `twrp-14.1` (Android 14+), `twrp-12.1`, `twrp-11.0` |
| `DEVICE_TREE_URL` | URL вашего дерева устройства | `https://github.com/grib24/twrp_device_realme_RE6054` |
| `DEVICE_TREE_BRANCH` | Ветка дерева | `main` |
| `DEVICE_PATH` | Путь для клонирования дерева | `device/realme/RE6054` |
| `DEVICE_NAME` | Кодовое имя устройства | `RE6054` |
| `MAKEFILE_NAME` | Префикс продукта | `twrp` |
| `BUILD_TARGET` | Что собирать | `vendorboot` (A/B + vendor_boot), `boot` (без раздела recovery), `recovery` (старые устройства) |

4. После завершения результат появится в разделе **Releases** репозитория с тегом `twrp-<run_id>`.
5. Внутри релиза:
   - `vendor_boot.img` / `boot.img` / `recovery.img` — образы (в зависимости от `BUILD_TARGET`).
   - Описание сборки: ветка манифеста, ссылка на дерево, коммит.

## Использование как Composite Action

Если вы хотите вызывать сборку из **другого** workflow, используйте `action.yml`:

```yaml
- name: Recovery Compilation
  uses: grib24/Recovery-Compiler@beta
  env:
    MANIFEST: "https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp -b twrp-14.1"
    DT_LINK: "https://github.com/grib24/twrp_device_realme_RE6054 -b main"
    VENDOR: "realme"
    CODENAME: "RE6054"
    KERNEL_LINK: ""
    TARGET: "vendorbootimage"
    FLAVOR: "eng"
    EXTRA_CMD: ""
    TZ: "Europe/Moscow"
