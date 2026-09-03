# Render + GitHub + MongoDB + Uptime সেটআপ

এই প্যাকেজে bot-এর secret source code থেকে সরানো হয়েছে। SQLite-এর existing
query layer রাখা হয়েছে, কিন্তু `MONGODB_URI` দিলে SQLite snapshot এবং user-uploaded
source files MongoDB GridFS-এ sync হবে—তাই Render restart-এর পর data ফেরত আসবে।

## GitHub

1. `bot.py`, `requirements.txt`, `Dockerfile`, `render.yaml`, এবং `.gitignore`
   repository-র root-এ রাখুন।
2. Telegram token বা Binance key কোনো commit-এ রাখবেন না।
3. GitHub-এ push করার আগে আগের exposed Telegram token অবশ্যই BotFather থেকে
   revoke করে নতুন token নিন।

## MongoDB Atlas

একটি database user তৈরি করে Network Access-এ Render-এর outbound access অনুমোদন
করুন। Render-এ `MONGODB_URI` হিসেবে Atlas-এর SRV connection string দিন।
`MONGODB_DB_NAME` সাধারণত `hostylity_bot` রাখতে পারেন।

## Render

GitHub repository দিয়ে **New Web Service** তৈরি করুন। `render.yaml` ব্যবহার করলে
Docker runtime এবং `/healthz` health check নিজে সেট হবে। Environment Variables-এ
কমপক্ষে এগুলো দিন:

- `BOT_TOKEN`
- `MONGODB_URI`
- `OWNER_ID`
- `ADMIN_ID`
- `YOUR_USERNAME`
- `UPDATE_CHANNEL`

Binance payment ব্যবহার করলে `BINANCE_API_KEY`, `BINANCE_SECRET_KEY`, এবং
`BINANCE_PAY_ID`-ও দিন। Deploy command আলাদা করে দিতে হবে না; Dockerfile
`python bot.py` চালাবে।

## UptimeRobot / অন্য uptime service

Deploy শেষ হলে Render-এর public URL-এর শেষে `/healthz` যোগ করে HTTP monitor দিন:

`https://YOUR-RENDER-SERVICE.onrender.com/healthz`

শুধু `/` নয়, `/healthz` monitor করুন—MongoDB disconnected হলে endpoint `503`
দেবে এবং uptime monitor সমস্যাটি ধরতে পারবে।