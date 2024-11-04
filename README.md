# 支援繁體中文（台灣）之 Calibre KFX Output plugin
由 @lcandy2 修改自 John Howells

最新版本2.11.0，請於「[Release](https://github.com/lcandy2/calibre-kfx-output-fix-traditional-chinese/releases)」頁面中下載該擴充。

### 效果預覽
<img src="https://github.com/user-attachments/assets/fd19382e-7952-47c0-8536-718145ae4b6d" height="480" />

### 已測試支援格式
- EPUB

### 特別注意
- 原始EPUB檔中`<dc:language>`標籤中的語言代碼需為`zh-hant`或`zh`，請勿使用`zh-TW`或`zh-CN`。
- 原始EPUB檔中存放文字的`html`或`xhtml`文件頭部需包含`xml:lang="zh-TW" lang="zh-TW"`，全部檔案中至少應有半數以上的文件符合此條件，請勿使用`zh-hant`，`zh-CN`或`zh`。

  正確的範例如下：
  ```html
  <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="zh-TW" lang="zh-TW">
  ```
  或如下形式：
  ```xhtml
  <html xmlns="http://www.w3.org/1999/xhtml" xmlns:epub="http://www.idpf.org/2007/ops" xml:lang="zh-TW" lang="zh-TW">
  ```

  確定`xml:lang`和`lang`屬性值皆為`zh-TW`，且位於文件中。

### 故障排除
- 通過calibre之Send to Kindle方式傳送的文件，可能會因為calibre導致檔案不能被Kindle辨識為繁體中文。若遇到此問題，請在Send to Kindle後，再次將原始kfx檔拷貝至Kindle設備中，替換calibre之kfx檔案。

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