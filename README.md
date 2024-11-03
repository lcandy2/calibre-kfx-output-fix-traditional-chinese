# 支援繁體中文（台灣）之 Calibre KFX Output plugin
由 @lcandy2 修改自 John Howells

最新版本2.11.0，請於「[Release](https://github.com/lcandy2/calibre-kfx-output-fix-traditional-chinese/releases)」頁面中下載該擴充。

### 效果預覽
<img src="https://github.com/user-attachments/assets/fd19382e-7952-47c0-8536-718145ae4b6d" height="480" />

### 已測試支援格式
- EPUB

### 變更之程式碼

於 `/kfxlib/original_source_epub.py` 第 628 行（在 2.11.0 版本中），加入以下程式碼：

```python
if FIX_LANGUAGE_SUFFIX and "-" not in lang.text:
    current_language_pattern = re.compile(re.escape(lang.text) + "(-.+)?$", re.IGNORECASE)
    best_language_variant, best_language_count = lang.text, 0

    for language, count in self.content_languages.items():
        if count > best_language_count and re.match(current_language_pattern, language):
            best_language_variant, best_language_count = language, count

    if best_language_variant != lang.text:

        # 新增程式碼從此開始 #
        if best_language_variant.lower() == "zh-tw":
            best_language_variant = "zh-hant"
            log.info("Optimize language from zh-TW to zh-hant")
        # 新增程式碼從此結束 #
        
        log.info("Changed EPUB language from '%s' to '%s'" % (lang.text, best_language_variant))
        lang.text = best_language_variant
        fixed = True
```

將語言代碼轉化為`zh-hant`以獲得Kindle內建繁體中文支援，包括標點符號置中，繁體字體選擇。

以上所有變更

### 許可 LICENSE

本方法撰寫於GitHub之「[lcandy2/calibre-kfx-output-fix-traditional-chinese](https://github.com/lcandy2/calibre-kfx-output-fix-traditional-chinese)」存儲庫，該README.md文件遵循「[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh-hant)」許可，除該文件之外其他位於存儲庫中的程式碼文件，遵循原作者「John Howell」之「GPL v3」許可。