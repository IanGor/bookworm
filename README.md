# BookWorm

Читалка FB2 и EPUB для Android. Разбор книг, типографика, переносы и отрисовка страниц находятся в общем Rust-проекте [BookwormEngine](https://github.com/IanGor/bookworm-engine), который также использует терминальная читалка [BookwormCLI](https://github.com/IanGor/bookworm-cli). Для локальной сборки разместите `BookwormEngine/` рядом с `Bookworm/`:

```
git clone https://github.com/IanGor/bookworm-engine BookwormEngine
git clone https://github.com/IanGor/bookworm Bookworm
```

- `crates/android` — JNI-мост к общему движку.
- `android/` — приложение на Kotlin без AndroidX: библиотека, читалка, настройки, сведения о книге.
- `tools/build_apk.sh` — сборка APK со шрифтами из общего движка.
- `docs/SPEC.md` — спецификация и принятые решения, `docs/STATUS.md` — текущее состояние.

## Сборка

```
cargo test --manifest-path ../BookwormEngine/Cargo.toml --workspace --locked
cargo run --manifest-path ../BookwormEngine/Cargo.toml --release -p bw-render -- \
    ../BookwormEngine/tests/corpus/synthetic/02_structure_ru.fb2 --family 'PT Serif' \
    --mono ../BookwormEngine/fonts/LiberationMono-Regular.ttf --pages 1-3
gradle -p android testReleaseUnitTest                  # тесты выделения текста
ABIS=arm64-v8a tools/build_apk.sh                      # нужны Android SDK + NDK, cargo-ndk, Gradle
```

В настройках можно включить бионическое чтение: начальная треть каждого слова выделяется
более плотным начертанием. Для просмотра на компьютере добавьте `--bionic` к команде `bw-render`.

Долгое нажатие выделяет слово прямо на странице; маркеры позволяют расширять выделение
через любое число абзацев, страниц и глав. Удерживайте маркер у верхнего или нижнего края,
чтобы продолжить выделение за экраном. Прокрутка, свайпы страниц и кнопки громкости
позволяют перемещаться по книге, сохраняя диапазон. В контекстном меню доступны
«Копировать», «Выделить всё» (книгу) и «Ошибка».
«Ошибка» сохраняет TXT-промпт с выбранным фрагментом, комментарием и данными для воспроизведения. В сведениях о книге можно
отметить её прочитанной; фильтр библиотеки скрывает отмеченные книги.

APK получается в `out/BookWorm.apk` (подписан отладочным ключом). Книг в приложении и в репозитории нет:
добавляйте свои файлы кнопкой «+» или через «Поделиться».

## Лицензия

Apache License 2.0, см. `LICENSE`. Сторонние компоненты и шрифты — в `NOTICE`.
