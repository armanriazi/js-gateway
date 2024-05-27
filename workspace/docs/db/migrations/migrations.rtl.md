
- جدول apis  مهمترین جدول چند رابطه ای دیتابیس می باشد.
- تمامی جداول کد فایل های این دسته شامل دو تابع  up , down می باشد. در بخش  up برای اعمال تغییرات روی جدول می باشد اما بخش down برای  پاک کردن تغییرات موجود در جدول می باشد.
- دو ستون ذیل در تمامی جداول وجود دارد و لذا در معرفی هر جدول دیتابیسی از توضیح آن ممانعت بعمل آمده است. ضمن آنکه هر دو ستون غیر قابل null می باشد.
- [x] createdAt
- [x] updatedAt

```md
    "db:create:seed": "npx sequelize-cli  seed:generate --name",
    "db:run:seed": "npx sequelize-cli  db:seed:all",
    "db:undo:seed": "npx sequelize-cli  db:seed:undo",
```
