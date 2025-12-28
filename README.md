##Cloudflare Tunnel တည်ဆောက်နည်း လမ်းညွှန် (macOS / Linux)

#ဒီလမ်းညွှန်မှာ Cloudflare Tunnel ကို command line (terminal) နဲ့ အဆင့်ဆင့် တည်ဆောက်ပုံကို ရှင်းပြထားပါတယ်။
#အဓိက အားဖြင့် macOS နဲ့ Linux အသုံးပြုသူတွေအတွက် ဖြစ်ပါတယ်။

1. cloudflared ထည့်သွင်းပါ

cloudflared CLI tool ကို အရင်ထည့်ပါ။

macOS 
```
brew install cloudflare/cloudflare/cloudflared
```

သို့မဟုတ် လက်နဲ့ ထည့်သွင်းပါ (macOS ARM64 ဥပမာ)
```
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-darwin-arm64
```
```
chmod +x cloudflared-darwin-arm64
```
```
sudo mv cloudflared-darwin-arm64 /usr/local/bin/cloudflared
```

Linux သုံးသူတွေ ကျေးဇူးပြု၍ အောက်ပါ link ကနေ သင့် OS နဲ့ ကိုက်ညီတဲ့ file ကို ဒေါင်းပါ
```
https://github.com/cloudflare/cloudflared/releases
```

ထည့်ပြီးစစ်ပါ။
```
cloudflared --version
```


2. နမူနာ config ဖိုင်တွေ ရယူပါ
```
git clone https://github.com/Mnaw161329/cloudflared.git
```
ဒီဖိုလ်ဒါထဲမှာ နမူနာ config တွေနဲ့ အသုံးဝင်တဲ့ ဖိုင်တွေ ပါပါတယ်။


3. လိုအပ်တဲ့ ဖိုင်တွေကို ~/.cloudflared/ ထဲ ရွှေ့ပါ

ဖိုလ်ဒါ မရှိရင် ဖန်တီးပါ
```
mkdir -p ~/.cloudflared
```

cloned folder ထဲက ဖိုင်တွေ ကူးပါ (လိုသလို ရွေးပါ)
```
cp cloudflared/* ~/.cloudflared/
```


4. cert.pem ပြင်ဆင်ခြင်း
```
nano ./cloudflared/cert.pem
```
```
cp ./cloudflared/cert.pem ~/.cloudflared/
```
အောင်မြင်ရင် ~/.cloudflared/ ထဲမှာ cert.pem ဖန်တီးပေးပါလိမ့်မယ်။


5. Tunnel ဖန်တီးပါ

နာမည်ပေးပြီး tunnel တစ်ခု ဖန်တီးပါ။
cloudflared tunnel create သင့်-tunnel-နာမည်

ဥပမာ။
```
cloudflared tunnel create www(subdomain)
```
အောင်မြင်ရင် Tunnel UUID နဲ့ credentials file တစ်ခု ဖန်တီးပေးပါလိမ့်မယ်။
ဥပမာ။ ~/.cloudflared/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.json


6. Config file ဖန်တီးပါ
```
nano ~/Desktop/cloudflared/yourTunnel-config.yml
```

cloned folder ထဲက နမူနာကို ကူးပြီး ပြင်ဆင်ပါ။

အရေးကြီးဆုံး အပိုင်း။
```
tunnel: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx   # သင့် tunnel UUID သို့မဟုတ် နာမည်
credentials-file: /Users/သင့်အမည်/.cloudflared/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.json

ingress:
  - hostname: www.yourdomain.com
    service: http://localhost:80               # သင့် local server port နဲ့ ပြောင်းပါ (ဥပမာ 3000, 8080)
  - hostname: yourdomain.com                   # apex domain လိုရင် ထည့်ပါ
    service: http://localhost:80
  - service: http_status:404                   # အမြဲ နောက်ဆုံး ထားရမယ် (catch-all rule)
```
Save လုပ်ပါ။ (nano မှာ Ctrl+O → Enter → Ctrl+X)

Config မှန်မမှန် စစ်ပါ။
```
cloudflared tunnel --config ~/Desktop/cloudflared/yourTunnel-config.yml ingress validate
```


7. DNS route ထည့်ပါ

Cloudflare DNS ထဲမှာ အလိုအလျောက် CNAME record ဖန်တီးပေးပါတယ်။
cloudflared tunnel route dns သင့်-tunnel-နာမည် subdomain.yourdomain.com

ဥပမာ။
```
cloudflared tunnel route dns www(subdomain) subdomain.yourdomain.com
```


8. Tunnel ကို ဖွင့်ပါ
cloudflared tunnel --config ~/Desktop/cloudflared/yourTunnel-config.yml run သင့်-tunnel-နာမည်

ဥပမာ။
```
cloudflared tunnel --config ~/Desktop/cloudflared/www-config.yml run www
```
