# تطبيق فانتوم (Phantom) — دليل البناء الكامل من الصفر إلى التسليم

> **طريقة الاستخدام:** انسخ هذا الملف بالكامل وأرسله للذكاء الاصطناعي (الذي سيبني التطبيق) مع جملة واحدة: *"نفّذ هذا الملف حرفياً، مرحلة بعد مرحلة، وسلّمني كل ملف كاملاً بدون اختصار."*
> الأكواد والأوامر بالإنجليزية كما هي، والشرح بالعربية.

---

## 0. قواعد صارمة للذكاء الاصطناعي (اقرأها أولاً)

1. التطبيق **مستقل تماماً** عن بقية التطبيقات (ملعبنا، توقعاتي، xwin، منصة سراب...). ممنوع استخدام أي مفتاح أو رابط أو معرّف من مشروع آخر.
2. مشروع **Supabase جديد بالكامل**، ومشروع **EAS جديد**، و**هوية تطبيق جديدة** (`slug` و`package` و`bundleIdentifier` فريدة).
3. **ممنوع ترقية** إصدار Expo SDK أو React Native أو أي حزمة خارج الإصدارات المحددة في القسم 2. إذا ظهر تعارض فاستخدم `npx expo install <package>` ولا تستخدم `npm install` للحزم المرتبطة بـ Expo.
4. اكتب **كل ملف كاملاً** من أول سطر لآخر سطر. ممنوع كتابة `// ... بقية الكود` أو `TODO` أو أي اختصار.
5. لا تُنشئ ملفات Excel أو PowerPoint أو HTML. المخرجات هي ملفات المشروع (JS/JSX/JSON/SQL/MD) فقط.
6. لا تضع **service_role key** أبداً داخل التطبيق. المفتاح الوحيد المسموح هو **anon (publishable) key**.
7. لا تعتمد على Supabase Auth. نظام الدخول **مخصص** (اسم مستخدم + كلمة مرور) كما في توقعاتي، لكن بطريقة آمنة عبر دوال RPC (القسم 7).
8. كل استدعاء لقاعدة البيانات يمر عبر ملف واحد فقط: `src/lib/api.js`. ممنوع استدعاء `supabase` مباشرة من الشاشات.
9. التطبيق عربي **RTL** بالكامل.
10. بعد الانتهاء نفّذ **قائمة القبول** (القسم 15) وأعد النتيجة بنعم/لا لكل بند.

---

## 1. نظرة عامة على التطبيق

**فانتوم** تطبيق توقعات رياضية بنفس هيكل تطبيق **توقعاتي**، مع التحديثات التالية:

| الميزة | الوصف |
|---|---|
| صندوق الرصيد | صندوق رابع في الشاشة الرئيسية بجانب (المسابقة، لوحة الصدارة، التحدي) يعرض رصيد المستخدم |
| سجل الرصيد | الضغط على الصندوق يفتح نافذة (Modal) فيها سجل النقاط/الحركات لكل مباراة |
| الشحن بالكود | حقل للمبلغ + اختيار وسيلة الدفع (زين كاش، آسيا باي، ماستركارد) + صندوق كود/إحالة + زر **"إدخال"** |
| التوثيق | نجاح الشحن بكود صحيح يجعل الحساب **موثّقاً** |
| النجوم | المستخدم الموثّق يختار نجمة **حمراء أو خضراء أو صفراء** تظهر بجانب اسمه في لوحة الصدارة والمسابقات |
| الأودز اليدوية | يحددها **المدير الفني** و**نائب المدير** يدوياً لكل مباراة (بدون أي مصدر خارجي)، وتُعرض للمستخدم قبل تأكيد التوقع |
| سقف الأودز | بين **1x و 2x** |
| الشكل | تصميم مختلف تماماً عن توقعاتي (القسم 12) |

---

## 2. التقنيات والإصدارات (مثبّتة، لا تغيّرها)

| العنصر | الإصدار |
|---|---|
| Expo SDK | **51** (`expo ~51.0.x`) |
| React Native | **0.74.5** |
| React | **18.2.0** |
| Expo Router | **~3.5.x** |
| إدارة الحالة | **Zustand** (`^4.5`) |
| تخزين الجلسة | `@react-native-async-storage/async-storage` (المتوافق مع SDK 51) |
| قاعدة البيانات | **Supabase** (`@supabase/supabase-js ^2`) + `react-native-url-polyfill` |
| الخط | Cairo عبر `@expo-google-fonts/cairo` + `expo-font` |
| البناء | **EAS Build** |
| اللغة | JavaScript (JSX) وليس TypeScript |

> **ملاحظة:** SDK 51 يستهدف Android API 34. للتوزيع المباشر (APK) لا مشكلة. أما عند الرفع على Google Play فقد تفرض المتاجر مستوى API أعلى، فتحقق من المتطلبات الحالية قبل النشر.

---

## 3. المتطلبات المسبقة (على جهازك)

```bash
node -v          # يفضّل Node 18 أو 20 LTS
npm -v
git --version
```

- حساب في **expo.dev**.
- حساب في **supabase.com**.
- محرر **Visual Studio Code**.

---

## 4. إنشاء المشروع وتثبيت الحزم

```bash
npx create-expo-app@latest phantom --template blank@sdk-51
cd phantom

# حزم Expo (تُثبَّت بالإصدار المتوافق تلقائياً)
npx expo install expo-router@~3.5.23 react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar expo-font @react-native-async-storage/async-storage

# حزم عامة
npm install zustand @supabase/supabase-js react-native-url-polyfill @expo-google-fonts/cairo

# تصحيح أي إصدار غير متوافق
npx expo install --fix
```

**تعديلات إلزامية بعد الإنشاء:**

1. احذف `App.js` و `index.js` من جذر المشروع.
2. في `package.json` غيّر السطر:
   ```json
   "main": "expo-router/entry"
   ```
3. أنشئ `babel.config.js`:
   ```js
   module.exports = function (api) {
     api.cache(true);
     return { presets: ['babel-preset-expo'] };
   };
   ```

---

## 5. هيكل الملفات

```
phantom/
├─ app/
│  ├─ _layout.jsx              # الجذر + AuthGuard + الخطوط + RTL
│  ├─ index.jsx                # الشاشة الرئيسية (4 صناديق)
│  ├─ login.jsx
│  ├─ register.jsx
│  ├─ competition.jsx          # المسابقة: قائمة المباريات + التوقع
│  ├─ leaderboard.jsx
│  ├─ rivalry.jsx
│  ├─ match/[id].jsx           # تفاصيل مباراة + شاشة تأكيد التوقع
│  └─ admin/
│     ├─ index.jsx             # لوحة الإدارة (للمدير الفني ونائبه فقط)
│     ├─ matches.jsx           # إنشاء مباراة + تحديد الأودز + إنهاء المباراة
│     └─ codes.jsx             # توليد أكواد الشحن
├─ src/
│  ├─ constants/config.js      # COLORS و SUPABASE_URL و SUPABASE_ANON_KEY
│  ├─ lib/supabase.js          # إنشاء العميل فقط
│  ├─ lib/api.js               # المحوّل الوحيد لكل استدعاءات Supabase
│  ├─ store/authStore.js       # Zustand: المستخدم والجلسة
│  ├─ store/appStore.js        # Zustand: المباريات والرصيد
│  └─ components/
│     ├─ BalanceBox.jsx
│     ├─ WalletModal.jsx       # سجل الحركات + الشحن
│     ├─ TopUpForm.jsx
│     ├─ StarBadge.jsx
│     ├─ StarPicker.jsx
│     ├─ OddsPill.jsx
│     ├─ MatchCard.jsx
│     ├─ ConfirmPredictionSheet.jsx
│     ├─ MainBox.jsx
│     └─ Button.jsx, Input.jsx, Screen.jsx
├─ assets/ (icon.png, adaptive-icon.png, splash.png)
├─ supabase/schema.sql         # القسم 7 كاملاً
├─ app.json
├─ eas.json
├─ babel.config.js
└─ package.json
```

---

## 6. ملفات الإعدادات (اللبنة الأساسية)

### 6.1 `app.json`

> غيّر ما بين `<>` فقط. **`package` و`bundleIdentifier` و`slug` يجب أن تكون فريدة ومختلفة عن كل تطبيقاتك الأخرى.**

```json
{
  "expo": {
    "name": "فانتوم",
    "slug": "phantom-app",
    "scheme": "phantom",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "userInterfaceStyle": "dark",
    "splash": {
      "image": "./assets/splash.png",
      "resizeMode": "contain",
      "backgroundColor": "#0B0B14"
    },
    "assetBundlePatterns": ["**/*"],
    "plugins": ["expo-router"],
    "android": {
      "package": "com.<yourname>.phantom",
      "versionCode": 1,
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#0B0B14"
      }
    },
    "ios": {
      "bundleIdentifier": "com.<yourname>.phantom",
      "supportsTablet": false
    },
    "extra": {
      "eas": {
        "projectId": "<يُملأ تلقائياً بعد الأمر eas init>"
      }
    }
  }
}
```

قواعد مهمة:
- لا تنسخ `projectId` من تطبيق آخر أبداً. الأمر `eas init` ينشئ واحداً جديداً.
- لا تضف `owner` إلا إذا كنت تبني تحت منظمة في Expo.
- `plugins` يجب أن تحتوي `expo-router` (وإلا لن يعمل التوجيه).

### 6.2 `eas.json`

> إذا كان `eas.json` في ملعبنا أو توقعاتي يبني عندك بنجاح، فانسخه كما هو. هذا هو المرجع:

```json
{
  "cli": {
    "version": ">= 10.0.0",
    "appVersionSource": "remote"
  },
  "build": {
    "preview": {
      "distribution": "internal",
      "android": { "buildType": "apk" }
    },
    "production": {
      "autoIncrement": true,
      "android": { "buildType": "app-bundle" }
    }
  },
  "submit": {
    "production": {}
  }
}
```

- **preview** ينتج ملف **APK** تثبّته مباشرة على الهاتف.
- **production** ينتج **AAB** للمتاجر.

### 6.3 `babel.config.js`
موجود في القسم 4.

---

## 7. قاعدة البيانات: مشروع Supabase جديد

### 7.1 الإنشاء

1. supabase.com ← **New project** ← اسمه `phantom` ← اختر منطقة قريبة ← كلمة مرور قوية لقاعدة البيانات واحفظها.
2. من **Project Settings → API** انسخ: `Project URL` و `anon public key` (فقط).
3. من **SQL Editor** شغّل المحتوى التالي كاملاً (احفظه أيضاً في `supabase/schema.sql`).

### 7.2 الأمان: لماذا RPC؟

التطبيق يستخدم مفتاح anon العام، لذلك **لا يجوز** أن تقرأ الجداول مباشرة (فيها كلمات المرور والأرصدة). الحل:
- تفعيل **RLS** على كل الجداول **بدون أي policy** ← المفتاح العام لا يستطيع القراءة أو الكتابة مباشرة.
- كل العمليات تتم عبر **دوال Postgres** بصلاحية `SECURITY DEFINER` تتحقق من الجلسة (token) والدور.

### 7.3 الجداول

```sql
create extension if not exists pgcrypto with schema extensions;

-- المستخدمون
create table public.prediction_users (
  id            uuid primary key default gen_random_uuid(),
  username      text not null unique check (char_length(username) between 3 and 24),
  password_hash text not null,
  display_name  text not null check (char_length(display_name) between 2 and 32),
  role          text not null default 'user' check (role in ('user','deputy','technical_manager')),
  balance       numeric(12,2) not null default 0 check (balance >= 0),
  is_verified   boolean not null default false,
  star_color    text check (star_color in ('red','green','yellow')),
  created_at    timestamptz not null default now(),
  -- النجمة لا تكون إلا لحساب موثّق
  constraint star_requires_verified check (star_color is null or is_verified)
);

-- الجلسات
create table public.app_sessions (
  token      uuid primary key default gen_random_uuid(),
  user_id    uuid not null references public.prediction_users(id) on delete cascade,
  created_at timestamptz not null default now(),
  expires_at timestamptz not null default now() + interval '30 days'
);

-- المسابقات
create table public.competitions (
  id         uuid primary key default gen_random_uuid(),
  name       text not null,
  is_active  boolean not null default true,
  created_at timestamptz not null default now()
);

-- المباريات (الأودز يدوية، من 1.00 إلى 2.00)
create table public.matches (
  id             uuid primary key default gen_random_uuid(),
  competition_id uuid references public.competitions(id) on delete set null,
  home_team      text not null,
  away_team      text not null,
  kickoff_at     timestamptz not null,
  status         text not null default 'open' check (status in ('open','closed','settled','cancelled')),
  home_score     int,
  away_score     int,
  odds_home      numeric(4,2) check (odds_home between 1.00 and 2.00),
  odds_draw      numeric(4,2) check (odds_draw between 1.00 and 2.00),
  odds_away      numeric(4,2) check (odds_away between 1.00 and 2.00),
  odds_set_by    uuid references public.prediction_users(id),
  created_at     timestamptz not null default now()
);

-- التوقعات
create table public.predictions (
  id                 uuid primary key default gen_random_uuid(),
  user_id            uuid not null references public.prediction_users(id) on delete cascade,
  match_id           uuid not null references public.matches(id) on delete cascade,
  pick               text not null check (pick in ('home','draw','away')),
  stake              numeric(12,2) not null check (stake > 0),
  odds_at_prediction numeric(4,2) not null check (odds_at_prediction between 1.00 and 2.00),
  status             text not null default 'pending' check (status in ('pending','won','lost','refunded')),
  payout             numeric(12,2) not null default 0,
  created_at         timestamptz not null default now(),
  unique (user_id, match_id)
);

-- أكواد الشحن (كل كود يُستخدم مرة واحدة)
create table public.topup_codes (
  id         uuid primary key default gen_random_uuid(),
  code       text not null unique,
  amount     numeric(12,2) not null check (amount > 0),
  is_used    boolean not null default false,
  used_by    uuid references public.prediction_users(id),
  used_at    timestamptz,
  created_by uuid references public.prediction_users(id),
  created_at timestamptz not null default now()
);

-- سجل الحركات المالية (مصدر الحقيقة لسجل الرصيد)
create table public.wallet_transactions (
  id            uuid primary key default gen_random_uuid(),
  user_id       uuid not null references public.prediction_users(id) on delete cascade,
  type          text not null check (type in ('topup','stake','payout','refund')),
  amount        numeric(12,2) not null,          -- موجب دائماً، النوع يحدد الاتجاه
  balance_after numeric(12,2) not null,
  method        text check (method in ('zain_cash','asia_pay','mastercard')),
  match_id      uuid references public.matches(id) on delete set null,
  code_id       uuid references public.topup_codes(id) on delete set null,
  note          text,
  created_at    timestamptz not null default now()
);

-- التحدي بين مستخدمين
create table public.rivalries (
  id            uuid primary key default gen_random_uuid(),
  challenger_id uuid not null references public.prediction_users(id) on delete cascade,
  opponent_id   uuid not null references public.prediction_users(id) on delete cascade,
  status        text not null default 'pending' check (status in ('pending','active','declined','finished')),
  created_at    timestamptz not null default now(),
  check (challenger_id <> opponent_id)
);

-- فهارس
create index on public.predictions (user_id);
create index on public.predictions (match_id);
create index on public.wallet_transactions (user_id, created_at desc);
create index on public.matches (status, kickoff_at);

-- RLS مفعّل بدون policies = لا وصول مباشر بالمفتاح العام
alter table public.prediction_users     enable row level security;
alter table public.app_sessions         enable row level security;
alter table public.competitions         enable row level security;
alter table public.matches              enable row level security;
alter table public.predictions          enable row level security;
alter table public.topup_codes          enable row level security;
alter table public.wallet_transactions  enable row level security;
alter table public.rivalries            enable row level security;
```

### 7.4 الدوال (RPC)

> كلها `SECURITY DEFINER` مع `search_path` ثابت. الدالة `_uid` داخلية ولا تُمنح لـ anon.

```sql
-- التحقق من الجلسة
create or replace function public._uid(p_token uuid)
returns uuid language plpgsql security definer set search_path = public as $$
declare v uuid;
begin
  select user_id into v from app_sessions where token = p_token and expires_at > now();
  if v is null then raise exception 'SESSION_INVALID'; end if;
  return v;
end $$;

-- التحقق من أن المستخدم مدير فني أو نائب
create or replace function public._require_admin(p_token uuid)
returns uuid language plpgsql security definer set search_path = public as $$
declare v uuid; r text;
begin
  v := public._uid(p_token);
  select role into r from prediction_users where id = v;
  if r not in ('deputy','technical_manager') then raise exception 'FORBIDDEN'; end if;
  return v;
end $$;

-- تسجيل حساب جديد
create or replace function public.register(p_username text, p_password text, p_display_name text)
returns json language plpgsql security definer set search_path = public, extensions as $$
declare v_id uuid; v_token uuid;
begin
  if char_length(p_password) < 6 then raise exception 'PASSWORD_TOO_SHORT'; end if;
  if exists (select 1 from prediction_users where lower(username) = lower(p_username)) then
    raise exception 'USERNAME_TAKEN';
  end if;
  insert into prediction_users (username, password_hash, display_name)
  values (lower(p_username), crypt(p_password, gen_salt('bf')), p_display_name)
  returning id into v_id;
  insert into app_sessions (user_id) values (v_id) returning token into v_token;
  return json_build_object('token', v_token, 'user', public._me(v_id));
end $$;

-- بيانات المستخدم الحالية بدون كلمة المرور
create or replace function public._me(p_id uuid)
returns json language sql security definer set search_path = public as $$
  select json_build_object('id',id,'username',username,'display_name',display_name,
    'role',role,'balance',balance,'is_verified',is_verified,'star_color',star_color)
  from prediction_users where id = p_id
$$;

-- تسجيل الدخول
create or replace function public.login(p_username text, p_password text)
returns json language plpgsql security definer set search_path = public, extensions as $$
declare v_id uuid; v_token uuid;
begin
  select id into v_id from prediction_users
   where lower(username) = lower(p_username) and password_hash = crypt(p_password, password_hash);
  if v_id is null then raise exception 'BAD_CREDENTIALS'; end if;
  insert into app_sessions (user_id) values (v_id) returning token into v_token;
  return json_build_object('token', v_token, 'user', public._me(v_id));
end $$;

create or replace function public.get_me(p_token uuid)
returns json language plpgsql security definer set search_path = public as $$
begin return public._me(public._uid(p_token)); end $$;

create or replace function public.logout(p_token uuid)
returns void language sql security definer set search_path = public as $$
  delete from app_sessions where token = p_token
$$;

-- قائمة المباريات مع توقع المستخدم إن وُجد
create or replace function public.list_matches(p_token uuid, p_competition uuid default null)
returns json language plpgsql security definer set search_path = public as $$
declare v uuid := public._uid(p_token);
begin
  return coalesce((select json_agg(x order by x.kickoff_at) from (
    select m.*, p.pick as my_pick, p.stake as my_stake, p.status as my_status
    from matches m
    left join predictions p on p.match_id = m.id and p.user_id = v
    where (p_competition is null or m.competition_id = p_competition)
      and m.status in ('open','closed','settled')
  ) x), '[]'::json);
end $$;

-- تحديد الأودز يدوياً (مدير فني / نائب)
create or replace function public.set_odds(p_token uuid, p_match uuid, p_home numeric, p_draw numeric, p_away numeric)
returns void language plpgsql security definer set search_path = public as $$
declare v uuid := public._require_admin(p_token);
begin
  if least(p_home, p_draw, p_away) < 1 or greatest(p_home, p_draw, p_away) > 2 then
    raise exception 'ODDS_OUT_OF_RANGE';
  end if;
  update matches set odds_home = p_home, odds_draw = p_draw, odds_away = p_away, odds_set_by = v
   where id = p_match and status = 'open';
  if not found then raise exception 'MATCH_NOT_OPEN'; end if;
end $$;

-- وضع توقع (يخصم المبلغ ويثبّت الأودز وقت التوقع)
create or replace function public.place_prediction(p_token uuid, p_match uuid, p_pick text, p_stake numeric)
returns json language plpgsql security definer set search_path = public as $$
declare v uuid := public._uid(p_token); m matches%rowtype; u prediction_users%rowtype; o numeric;
begin
  if p_stake is null or p_stake <= 0 then raise exception 'BAD_STAKE'; end if;
  select * into m from matches where id = p_match for update;
  if m.id is null or m.status <> 'open' or m.kickoff_at <= now() then raise exception 'MATCH_CLOSED'; end if;
  o := case p_pick when 'home' then m.odds_home when 'draw' then m.odds_draw when 'away' then m.odds_away end;
  if o is null then raise exception 'ODDS_NOT_SET'; end if;
  select * into u from prediction_users where id = v for update;
  if u.balance < p_stake then raise exception 'INSUFFICIENT_BALANCE'; end if;
  insert into predictions (user_id, match_id, pick, stake, odds_at_prediction)
  values (v, p_match, p_pick, p_stake, o);   -- يفشل إن كان هناك توقع سابق (unique)
  update prediction_users set balance = balance - p_stake where id = v;
  insert into wallet_transactions (user_id, type, amount, balance_after, match_id)
  values (v, 'stake', p_stake, u.balance - p_stake, p_match);
  return public._me(v);
end $$;

-- إنهاء مباراة وتوزيع الأرباح (مدير فني / نائب)
create or replace function public.settle_match(p_token uuid, p_match uuid, p_home_score int, p_away_score int)
returns void language plpgsql security definer set search_path = public as $$
declare r text; p record; win numeric; newbal numeric;
begin
  perform public._require_admin(p_token);
  perform 1 from matches where id = p_match and status in ('open','closed') for update;
  if not found then raise exception 'MATCH_NOT_SETTLEABLE'; end if;
  r := case when p_home_score > p_away_score then 'home'
            when p_home_score < p_away_score then 'away' else 'draw' end;
  update matches set status='settled', home_score=p_home_score, away_score=p_away_score where id = p_match;
  for p in select * from predictions where match_id = p_match and status = 'pending' loop
    if p.pick = r then
      win := round(p.stake * p.odds_at_prediction, 2);
      update prediction_users set balance = balance + win where id = p.user_id returning balance into newbal;
      update predictions set status='won', payout=win where id = p.id;
      insert into wallet_transactions (user_id, type, amount, balance_after, match_id)
      values (p.user_id, 'payout', win, newbal, p_match);
    else
      update predictions set status='lost' where id = p.id;
    end if;
  end loop;
end $$;

-- توليد أكواد شحن (مدير فني / نائب)
create or replace function public.create_codes(p_token uuid, p_amount numeric, p_count int)
returns json language plpgsql security definer set search_path = public, extensions as $$
declare v uuid := public._require_admin(p_token); i int; c text; out_codes text[] := '{}';
begin
  if p_count < 1 or p_count > 100 or p_amount <= 0 then raise exception 'BAD_INPUT'; end if;
  for i in 1..p_count loop
    c := upper(substr(encode(gen_random_bytes(6), 'hex'), 1, 10));
    insert into topup_codes (code, amount, created_by) values (c, p_amount, v);
    out_codes := out_codes || c;
  end loop;
  return to_json(out_codes);
end $$;

-- إدخال كود الشحن: يزيد الرصيد ويوثّق الحساب
create or replace function public.redeem_code(p_token uuid, p_code text, p_method text)
returns json language plpgsql security definer set search_path = public as $$
declare v uuid := public._uid(p_token); c topup_codes%rowtype; newbal numeric;
begin
  if p_method not in ('zain_cash','asia_pay','mastercard') then raise exception 'BAD_METHOD'; end if;
  select * into c from topup_codes where code = upper(trim(p_code)) for update;
  if c.id is null then raise exception 'CODE_INVALID'; end if;
  if c.is_used then raise exception 'CODE_USED'; end if;
  update topup_codes set is_used = true, used_by = v, used_at = now() where id = c.id;
  update prediction_users set balance = balance + c.amount, is_verified = true
   where id = v returning balance into newbal;
  insert into wallet_transactions (user_id, type, amount, balance_after, method, code_id)
  values (v, 'topup', c.amount, newbal, p_method, c.id);
  return public._me(v);
end $$;

-- اختيار لون النجمة (لحساب موثّق فقط)
create or replace function public.set_star(p_token uuid, p_color text)
returns json language plpgsql security definer set search_path = public as $$
declare v uuid := public._uid(p_token);
begin
  if p_color not in ('red','green','yellow') then raise exception 'BAD_COLOR'; end if;
  update prediction_users set star_color = p_color where id = v and is_verified;
  if not found then raise exception 'NOT_VERIFIED'; end if;
  return public._me(v);
end $$;

-- سجل الحركات المالية (آخر 100)
create or replace function public.wallet_history(p_token uuid)
returns json language plpgsql security definer set search_path = public as $$
declare v uuid := public._uid(p_token);
begin
  return coalesce((select json_agg(x order by x.created_at desc) from (
    select t.id, t.type, t.amount, t.balance_after, t.method, t.created_at,
           m.home_team, m.away_team
    from wallet_transactions t left join matches m on m.id = t.match_id
    where t.user_id = v order by t.created_at desc limit 100) x), '[]'::json);
end $$;

-- لوحة الصدارة: الأكثر صحة ثم الأعلى ربحاً
create or replace function public.leaderboard(p_token uuid)
returns json language plpgsql security definer set search_path = public as $$
begin
  perform public._uid(p_token);
  return coalesce((select json_agg(x) from (
    select u.id, u.display_name, u.star_color,
           count(*) filter (where p.status='won')  as wins,
           count(*) filter (where p.status in ('won','lost')) as total,
           coalesce(sum(p.payout - p.stake) filter (where p.status in ('won','lost')), 0) as net_profit
    from prediction_users u left join predictions p on p.user_id = u.id
    group by u.id order by wins desc, net_profit desc limit 100) x), '[]'::json);
end $$;

-- منح التنفيذ للمفتاح العام (باستثناء الدوال الداخلية)
revoke all on function public._uid(uuid), public._require_admin(uuid), public._me(uuid) from public, anon, authenticated;
grant execute on function
  public.register(text,text,text), public.login(text,text), public.get_me(uuid), public.logout(uuid),
  public.list_matches(uuid,uuid), public.set_odds(uuid,uuid,numeric,numeric,numeric),
  public.place_prediction(uuid,uuid,text,numeric), public.settle_match(uuid,uuid,int,int),
  public.create_codes(uuid,numeric,int), public.redeem_code(uuid,text,text), public.set_star(uuid,text),
  public.wallet_history(uuid), public.leaderboard(uuid)
to anon;
```

**دوال إضافية يكتبها الذكاء الاصطناعي بنفس النمط** (نفس التحقق من `_uid`/`_require_admin`، `SECURITY DEFINER`، `search_path` ثابت، منح التنفيذ لـ anon):

| الدالة | المهمة |
|---|---|
| `create_match(token, competition, home, away, kickoff)` | إدارة: إنشاء مباراة |
| `list_competitions(token)` | قائمة المسابقات الفعالة |
| `create_competition(token, name)` | إدارة |
| `create_rivalry(token, opponent_username)` | إرسال تحدٍّ |
| `respond_rivalry(token, rivalry_id, accept bool)` | قبول/رفض |
| `list_rivalries(token)` | تحدياتي مع مقارنة النتائج |
| `set_role(token, user_id, role)` | **المدير الفني فقط**: تعيين نائب المدير |

**أول مدير فني:** سجّل حساباً من التطبيق ثم شغّل مرة واحدة في SQL Editor:
```sql
update public.prediction_users set role = 'technical_manager' where username = '<your_username>';
```

---

## 8. كود الأساس في التطبيق

### 8.1 `src/constants/config.js`

```js
export const SUPABASE_URL = 'https://<PROJECT_REF>.supabase.co';
export const SUPABASE_ANON_KEY = '<ANON_PUBLIC_KEY>';

export const COLORS = {
  bg: '#0B0B14',
  surface: '#14141F',
  surfaceAlt: '#1C1C2B',
  border: '#2A2A3D',
  primary: '#8B5CF6',
  primaryDim: '#6D43D6',
  accent: '#22D3EE',
  text: '#F4F4FB',
  textDim: '#9A9AB5',
  success: '#22C55E',
  danger: '#EF4444',
  warning: '#F59E0B',
  starRed: '#EF4444',
  starGreen: '#22C55E',
  starYellow: '#FACC15',
};

export const ODDS_MIN = 1;
export const ODDS_MAX = 2;
export const PAYMENT_METHODS = [
  { key: 'zain_cash', label: 'زين كاش' },
  { key: 'asia_pay', label: 'آسيا باي' },
  { key: 'mastercard', label: 'ماستركارد' },
];
```

### 8.2 `src/lib/supabase.js`

```js
import 'react-native-url-polyfill/auto';
import { createClient } from '@supabase/supabase-js';
import { SUPABASE_URL, SUPABASE_ANON_KEY } from '../constants/config';

export const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY, {
  auth: { persistSession: false, autoRefreshToken: false, detectSessionInUrl: false },
});
```

### 8.3 `src/lib/api.js` (المحوّل الوحيد)

```js
import { supabase } from './supabase';

const ERRORS = {
  SESSION_INVALID: 'انتهت الجلسة، سجّل الدخول من جديد',
  FORBIDDEN: 'ليست لديك صلاحية',
  USERNAME_TAKEN: 'اسم المستخدم مستخدم مسبقاً',
  PASSWORD_TOO_SHORT: 'كلمة المرور قصيرة (6 أحرف على الأقل)',
  BAD_CREDENTIALS: 'اسم المستخدم أو كلمة المرور غير صحيحة',
  MATCH_CLOSED: 'هذه المباراة مغلقة للتوقع',
  ODDS_NOT_SET: 'لم تُحدد الأودز لهذه المباراة بعد',
  INSUFFICIENT_BALANCE: 'رصيدك غير كافٍ',
  CODE_INVALID: 'الكود غير صحيح',
  CODE_USED: 'هذا الكود مستخدم مسبقاً',
  NOT_VERIFIED: 'يجب توثيق حسابك أولاً بالشحن',
  ODDS_OUT_OF_RANGE: 'الأودز يجب أن تكون بين 1 و 2',
  BAD_STAKE: 'مبلغ غير صالح',
};

async function call(fn, args = {}) {
  const { data, error } = await supabase.rpc(fn, args);
  if (error) {
    const key = Object.keys(ERRORS).find((k) => error.message?.includes(k));
    const err = new Error(key ? ERRORS[key] : 'حدث خطأ غير متوقع، حاول مرة أخرى');
    err.code = key || 'UNKNOWN';
    throw err;
  }
  return data;
}

export const api = {
  register: (username, password, displayName) =>
    call('register', { p_username: username, p_password: password, p_display_name: displayName }),
  login: (username, password) => call('login', { p_username: username, p_password: password }),
  getMe: (t) => call('get_me', { p_token: t }),
  logout: (t) => call('logout', { p_token: t }),
  listMatches: (t, competition = null) => call('list_matches', { p_token: t, p_competition: competition }),
  placePrediction: (t, match, pick, stake) =>
    call('place_prediction', { p_token: t, p_match: match, p_pick: pick, p_stake: stake }),
  walletHistory: (t) => call('wallet_history', { p_token: t }),
  redeemCode: (t, code, method) => call('redeem_code', { p_token: t, p_code: code, p_method: method }),
  setStar: (t, color) => call('set_star', { p_token: t, p_color: color }),
  leaderboard: (t) => call('leaderboard', { p_token: t }),
  // الإدارة
  setOdds: (t, match, h, d, a) =>
    call('set_odds', { p_token: t, p_match: match, p_home: h, p_draw: d, p_away: a }),
  settleMatch: (t, match, hs, as) =>
    call('settle_match', { p_token: t, p_match: match, p_home_score: hs, p_away_score: as }),
  createCodes: (t, amount, count) => call('create_codes', { p_token: t, p_amount: amount, p_count: count }),
  // + بقية الدوال (المسابقات، المباريات، التحديات، الأدوار) بنفس النمط
};
```

### 8.4 `src/store/authStore.js`

```js
import { create } from 'zustand';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { api } from '../lib/api';

const KEY = 'phantom_session_v1';

export const useAuth = create((set, get) => ({
  token: null,
  user: null,
  ready: false,

  hydrate: async () => {
    try {
      const raw = await AsyncStorage.getItem(KEY);
      if (raw) {
        const { token } = JSON.parse(raw);
        const user = await api.getMe(token);
        set({ token, user, ready: true });
        return;
      }
    } catch (e) {
      await AsyncStorage.removeItem(KEY);
    }
    set({ token: null, user: null, ready: true });
  },

  signIn: async (username, password) => {
    const { token, user } = await api.login(username, password);
    await AsyncStorage.setItem(KEY, JSON.stringify({ token }));
    set({ token, user });
  },

  signUp: async (username, password, displayName) => {
    const { token, user } = await api.register(username, password, displayName);
    await AsyncStorage.setItem(KEY, JSON.stringify({ token }));
    set({ token, user });
  },

  refreshUser: async () => {
    const t = get().token;
    if (t) set({ user: await api.getMe(t) });
  },

  setUser: (user) => set({ user }),

  signOut: async () => {
    const t = get().token;
    try { if (t) await api.logout(t); } catch (e) {}
    await AsyncStorage.removeItem(KEY);
    set({ token: null, user: null });
  },
}));
```

### 8.5 `app/_layout.jsx` (الجذر + AuthGuard + RTL + الخط)

المتطلبات:
- `I18nManager.allowRTL(true); I18nManager.forceRTL(true);` في أعلى الملف (خارج المكوّن).
- تحميل خط Cairo (`Cairo_400Regular` و`Cairo_700Bold`) بـ `useFonts`، وعدم عرض أي شيء حتى يجهز الخط وتنتهي `hydrate()`.
- `AuthGuard`: إذا لا يوجد `token` وكان المسار الحالي ليس `login`/`register` ← `router.replace('/login')`. وإذا يوجد `token` وكان المسار `login`/`register` ← `router.replace('/')`.
- مسار `/admin/*` لا يدخله إلا `role` = `deputy` أو `technical_manager`، وإلا يعاد للرئيسية.
- `StatusBar` بنمط `light`، وخلفية `COLORS.bg`، و`Stack` بدون رأس افتراضي (`headerShown: false`) مع رأس مخصص حيث يلزم.

---

## 9. الشاشات (المطلوب بالتفصيل)

### 9.1 تسجيل الدخول / إنشاء حساب
- حقول: اسم المستخدم، كلمة المرور (وللتسجيل: الاسم الظاهر).
- التحقق من الإدخال قبل الإرسال، عرض رسائل الخطأ من `api.js` بالعربي.
- مؤشر تحميل على الزر، ومنع الضغط المزدوج.

### 9.2 الرئيسية `app/index.jsx` — أربعة صناديق رئيسية
1. **المسابقة** ← `/competition`
2. **لوحة الصدارة** ← `/leaderboard`
3. **التحدي** ← `/rivalry`
4. **الرصيد** (`BalanceBox`) ← يعرض الرصيد الحالي، والضغط عليه يفتح `WalletModal`.

بجانب اسم المستخدم في الأعلى: `StarBadge` إن وُجدت نجمة. وزر لوحة الإدارة يظهر فقط للمدير الفني/النائب.

### 9.3 `WalletModal` (نافذة الرصيد)
- أعلى النافذة: الرصيد الحالي بخط كبير + شارة "موثّق/غير موثّق".
- **سجل الحركات** من `wallet_history`: لكل حركة (نوع، مبلغ بإشارة + أو −، اسم المباراة إن وُجد، التاريخ، الرصيد بعد الحركة). الأنواع: شحن / توقع / ربح / استرجاع بألوان مختلفة.
- زر **"شحن الرصيد"** يفتح `TopUpForm`.
- `StarPicker`: يظهر فقط للحساب الموثّق (ثلاث نجوم: حمراء، خضراء، صفراء) لاختيار نجمته، ويستدعي `set_star`.

### 9.4 `TopUpForm` (الشحن)
1. حقل **المبلغ**.
2. اختيار **وسيلة الدفع**: زين كاش / آسيا باي / ماستركارد.
3. صندوق **الكود / الإحالة**.
4. زر **"إدخال"** يستدعي `redeem_code`.

سلوك مهم: **لا يوجد ربط تلقائي ببوابات الدفع.** المستخدم يدفع خارج التطبيق ثم يستلم كوداً من الإدارة ويدخله. الرصيد المضاف هو **قيمة الكود** المخزنة في قاعدة البيانات (وليس الرقم الذي يكتبه المستخدم في حقل المبلغ، الذي يُستخدم للعرض فقط). نجاح الإدخال ← يتحدّث الرصيد، ويصبح الحساب موثّقاً، وتظهر رسالة نجاح مع دعوة لاختيار النجمة.

### 9.5 المسابقة والمباراة
- قائمة المباريات (`MatchCard`): الفريقان، موعد الانطلاق، الحالة، وثلاث أزرار نتيجة (فوز المضيف / تعادل / فوز الضيف) على كل زر `OddsPill` يعرض الأودز مثل `1.65x`.
- إذا لم تُحدد الأودز: الأزرار معطّلة مع عبارة "بانتظار تحديد الأودز".
- الضغط على نتيجة يفتح `ConfirmPredictionSheet`: يعرض **الأودز، المبلغ (حقل إدخال)، الربح المتوقع = المبلغ × الأودز، الرصيد بعد الخصم**، وزر "تأكيد التوقع". **الأودز يجب أن تظهر للمستخدم قبل التأكيد.**
- بعد التأكيد: يُحدَّث الرصيد من الرد، وتظهر حالة "توقعك مسجّل" على البطاقة (ولا يمكن تعديله).
- المباريات المغلقة/المنتهية تعرض نتيجة التوقع (ربحت/خسرت) والمبلغ.

### 9.6 لوحة الصدارة
- ترتيب (مرتب حسب الصحيحة ثم صافي الربح)، واسم المستخدم + `StarBadge` + عدد الصحيحة + صافي الربح.
- المستخدم الحالي مميّز بلون.

### 9.7 التحدي
- إرسال تحدٍّ لمستخدم باسم المستخدم، قائمة التحديات (معلّقة/فعالة)، قبول/رفض، ومقارنة نتائج الطرفين.

### 9.8 الإدارة (للمدير الفني والنائب)
- **المباريات:** إنشاء مباراة، تحديد الأودز الثلاث (إدخال رقمي مع تحقق 1.00–2.00 قبل الإرسال)، وإنهاء المباراة بإدخال النتيجة (يوزّع الأرباح تلقائياً).
- **الأكواد:** توليد أكواد بمبلغ وعدد محددين، عرضها قابلة للنسخ.
- **الأدوار (المدير الفني فقط):** تعيين نائب مدير.

---

## 10. قواعد المال والأودز (لا تُكسر)

1. الأودز تُحدد يدوياً فقط، بين **1.00 و 2.00**، من المدير الفني أو النائب. لا مصدر خارجي.
2. الأودز تُثبَّت في التوقع وقت التأكيد (`odds_at_prediction`)، وتغييرها لاحقاً لا يؤثر على التوقعات السابقة.
3. الربح عند الصحة = `المبلغ × الأودز` (يشمل المبلغ المدفوع). الخسارة = ضياع المبلغ.
4. توقع واحد لكل مستخدم في المباراة.
5. لا توقع بعد موعد الانطلاق أو بعد إغلاق المباراة.
6. كل حركة مالية تُسجَّل في `wallet_transactions` مع `balance_after`.
7. كل كود شحن يُستخدم **مرة واحدة فقط**.
8. جميع الحسابات المالية تتم في قاعدة البيانات (الدوال)، وليس في التطبيق. التطبيق يعرض فقط.

---

## 11. المسارات والتنقل

- Expo Router قائم على الملفات داخل `app/`.
- استخدم `router.push` للتنقل الأمامي و`router.replace` عند تسجيل الدخول/الخروج.
- الصفحات الديناميكية: `app/match/[id].jsx` وقراءة المعرّف بـ `useLocalSearchParams`.

---

## 12. التصميم (شكل مختلف تماماً عن توقعاتي)

> الألوان في `COLORS` (القسم 8.1) هي **قيم افتراضية** بطابع داكن بنفسجي/سماوي مناسب لاسم "فانتوم". غيّرها كلها من ملف واحد حسب الشكل الذي تريده، دون لمس بقية الكود.

الإرشادات:
- **كل الألوان** من `COLORS` فقط، ممنوع كتابة ألوان داخل الشاشات.
- الخلفية داكنة، البطاقات بحواف مدوّرة (16–20)، حدود رفيعة بلون `border`.
- الصناديق الأربعة الرئيسية: بطاقات كبيرة بأيقونة وعنوان، مع تدرّج لوني خفيف.
- الأودز في `OddsPill` بلون `accent`، والأرباح بلون `success`، والخسارة بلون `danger`.
- نص عربي بخط Cairo، حجم أساسي 15–16، والعناوين Bold.
- مساحات تنفّس واضحة (padding 16)، وأزرار بارتفاع 48 على الأقل.
- حالات التحميل (Skeleton أو ActivityIndicator) وحالات الفراغ برسائل عربية واضحة.
- `StarBadge`: أيقونة نجمة صغيرة بجانب الاسم بلون `starRed` / `starGreen` / `starYellow`.

---

## 13. بناء التطبيق وتثبيته (3 خطوات)

نفّذها من مجلد المشروع في Terminal داخل VS Code:

```bash
# 1) تثبيت الأداة
npm install -g eas-cli

# 2) تسجيل الدخول بحساب Expo
eas login

# 3) ربط المشروع (مرة واحدة فقط) ثم البناء
eas init
eas build -p android --profile preview
```

- `eas init` ينشئ مشروعاً جديداً على Expo ويكتب `projectId` داخل `app.json` تلقائياً.
- عند السؤال عن keystore اختر أن تنشئه EAS تلقائياً (Generate new keystore).
- عند انتهاء البناء يظهر رابط تحميل الـ APK.

للتجربة قبل البناء: `npx expo start`.

---

## 14. حل المشاكل الشائعة

| المشكلة | السبب والحل |
|---|---|
| فشل البناء بتعارض إصدارات | شغّل `npx expo install --fix` ثم أعد البناء |
| `Unable to resolve module` | حزمة ناقصة: `npx expo install <package>` |
| التطبيق يفتح على شاشة بيضاء | تأكد أن `"main": "expo-router/entry"` وأن `App.js` محذوف وأن plugin `expo-router` في `app.json` |
| `projectId` غير مطابق / مشروع خاطئ | احذف قيمة `extra.eas.projectId` وشغّل `eas init` من جديد |
| `package name already exists` | غيّر `android.package` لقيمة فريدة |
| خطأ `SESSION_INVALID` بعد فترة | الجلسة تنتهي بعد 30 يوماً، سجّل الدخول من جديد |
| الدوال لا تعمل (`function not found`) | تأكد من تشغيل `schema.sql` كاملاً ومن أقسام `grant execute` |
| RTL لا يظهر | أغلق التطبيق وافتحه من جديد (يُطبَّق بعد إعادة التشغيل) |
| `crypt` غير موجودة | تأكد من `create extension pgcrypto with schema extensions` وأن `search_path` يتضمن `extensions` |

---

## 15. قائمة القبول (يجيب عنها الذكاء الاصطناعي بنعم/لا)

1. هل المشروع يعمل بـ Expo SDK 51 وReact Native 0.74.5 دون تحذيرات إصدار؟
2. هل `app.json` يحتوي `slug` و`package` و`bundleIdentifier` فريدة ولا يحتوي `projectId` قديماً؟
3. هل `eas.json` مطابق للمرجع (preview = apk)؟
4. هل مشروع Supabase جديد ومنفصل، وهل المفتاح المستخدم هو anon فقط؟
5. هل RLS مفعّل على كل الجداول بلا policies، وكل الوصول عبر RPC؟
6. هل كل استدعاءات Supabase في `api.js` فقط؟
7. هل الدخول المخصص يعمل مع حفظ الجلسة في AsyncStorage؟
8. هل تظهر أربعة صناديق رئيسية، والرابع (الرصيد) يفتح النافذة؟
9. هل سجل الحركات يظهر لكل مباراة؟
10. هل الشحن بالكود يزيد الرصيد، ويوثّق الحساب، وكل كود لمرة واحدة؟
11. هل اختيار النجمة (أحمر/أخضر/أصفر) متاح للموثّق فقط وتظهر بجانب الاسم في الصدارة والمسابقات؟
12. هل الأودز تُحدد يدوياً بين 1 و2 وتظهر قبل التأكيد؟
13. هل إنهاء المباراة يوزّع الأرباح بشكل صحيح؟
14. هل الواجهة RTL بالكامل بخط Cairo؟
15. هل أوامر الخطوات الثلاث (`npm i -g eas-cli` → `eas login` → `eas build`) تبني APK بنجاح؟

---

## 16. تسليم العمل (ما يجب أن يعيده الذكاء الاصطناعي)

بهذا الترتيب:
1. أوامر الإنشاء والتثبيت (القسم 4).
2. `package.json` و`babel.config.js` و`app.json` و`eas.json` كاملة.
3. `supabase/schema.sql` كامل (الجداول + كل الدوال).
4. ملفات `src/` كاملة.
5. ملفات `app/` كاملة، شاشة بعد شاشة.
6. ما الذي يجب أن أضعه أنا يدوياً (رابط Supabase والمفتاح، أيقونات `assets/`، قيم `<yourname>`).
7. نتيجة قائمة القبول (القسم 15).

**ابدأ الآن من القسم 4.**
