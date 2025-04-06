import feedparser
import requests
import time
import json
import os
import hashlib
import re
from bs4 import BeautifulSoup
from datetime import datetime
from googletrans import Translator
from flask import Flask, render_template, request, redirect, url_for
from apscheduler.schedulers.background import BackgroundScheduler
from langdetect import detect

# تنظیمات منابع خبری
NEWS_SOURCES = {
    "etonline": "https://www.etonline.com/news/rss",
    "hollywoodreporter": "https://www.hollywoodreporter.com/t/rss.xml",
    "independentpersian": "https://www.independentpersian.com/rss",
    "alarabiya": "https://farsi.alarabiya.net/.mrss",
    "timesofisrael": "https://fa.timesofisrael.com/feed/",
    "radis": "https://radis.org/feed/"
}

# اضافه کردن الاین به عنوان وب‌سایت برای پارس کردن
WEBSITES_TO_SCRAPE = {
    "alainpersian": "https://alainpersian.com/"
}

# محل ذخیره‌سازی اخبار
DATA_DIR = "news_data"
os.makedirs(DATA_DIR, exist_ok=True)

# دسته‌بندی‌های خبری
NEWS_CATEGORIES = [
    "سیاسی", 
    "اقتصادی", 
    "فرهنگی", 
    "اجتماعی", 
    "ورزشی", 
    "علمی", 
    "فناوری", 
    "سلامت", 
    "سرگرمی", 
    "هنری", 
    "مد", 
    "بین‌المللی",
    "متفرقه"
]

# تنظیمات برنامه فلسک
app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key'

# ایجاد مترجم
translator = Translator()

# تحلیل متن برای دسته‌بندی خبر
def categorize_news(title, content):
    # کلمات کلیدی برای هر دسته
    keywords = {
        "سیاسی": ["سیاست", "دولت", "رئیس جمهور", "انتخابات", "مجلس", "وزیر", "رهبر", "حزب", "سیاسی", "پارلمان"],
        "اقتصادی": ["اقتصاد", "بورس", "بازار", "ارز", "دلار", "سهام", "تورم", "بانک", "اقتصادی", "مالی", "بیمه"],
        "فرهنگی": ["فرهنگ", "هنر", "موسیقی", "سینما", "تئاتر", "کتاب", "ادبیات", "فرهنگی", "جشنواره"],
        "اجتماعی": ["جامعه", "اجتماعی", "مردم", "شهروند", "خانواده", "آموزش", "دانشگاه", "مدرسه"],
        "ورزشی": ["ورزش", "فوتبال", "بسکتبال", "المپیک", "قهرمان", "مسابقه", "ورزشی", "تیم", "لیگ"],
        "علمی": ["علم", "پژوهش", "تحقیق", "دانشمند", "اختراع", "فضا", "علمی", "کشف"],
        "فناوری": ["فناوری", "تکنولوژی", "دیجیتال", "موبایل", "اینترنت", "کامپیوتر", "هوش مصنوعی", "اپلیکیشن"],
        "سلامت": ["سلامت", "بهداشت", "پزشکی", "دارو", "بیماری", "درمان", "ویروس", "واکسن"],
        "سرگرمی": ["سرگرمی", "بازی", "فیلم", "سریال", "تفریح", "سرگرم کننده", "شو", "سلبریتی"],
        "هنری": ["هنر", "نقاشی", "مجسمه", "گالری", "هنرمند", "نمایشگاه", "طراحی", "معماری"],
        "مد": ["مد", "فشن", "لباس", "طراح", "پوشاک", "استایل", "زیبایی", "مدل"],
        "بین‌المللی": ["بین‌المللی", "جهان", "کشور", "خارجی", "بین‌الملل", "قاره", "جهانی", "دیپلماسی", "سازمان ملل"]
    }
    
    # ترکیب عنوان و محتوا
    full_text = (title + " " + content).lower()
    
    # شمارش کلمات کلیدی هر دسته
    category_scores = {}
    for category, words in keywords.items():
        score = 0
        for word in words:
            score += full_text.count(word.lower())
        category_scores[category] = score
    
    # انتخاب دسته با بیشترین امتیاز
    if all(score == 0 for score in category_scores.values()):
        return "متفرقه"
    
    return max(category_scores, key=category_scores.get)

# فانکشن برای بررسی نیاز به ترجمه
def needs_translation(text):
    try:
        if not text:
            return False
        
        # بررسی زبان متن
        detected_lang = detect(text)
        return detected_lang != 'fa'
    except:
        # اگر تشخیص زبان با خطا مواجه شد، فرض می‌کنیم نیاز به ترجمه دارد
        return True

# ترجمه متن به فارسی
def translate_to_persian(text):
    if not text or not needs_translation(text):
        return text
    
    try:
        # تقسیم متن‌های طولانی به چند بخش برای ترجمه
        max_chunk_size = 4500  # محدودیت گوگل ترنسلیت
        if len(text) <= max_chunk_size:
            result = translator.translate(text, dest='fa').text
            return result
        
        # تقسیم متن طولانی به قطعات
        chunks = [text[i:i+max_chunk_size] for i in range(0, len(text), max_chunk_size)]
        translated_chunks = []
        
        for chunk in chunks:
            translated_chunk = translator.translate(chunk, dest='fa').text
            translated_chunks.append(translated_chunk)
            time.sleep(1)  # تاخیر برای جلوگیری از محدودیت API
            
        return ''.join(translated_chunks)
    except Exception as e:
        print(f"خطا در ترجمه: {e}")
        return text

# استخراج محتوای خبر از طریق RSS
def fetch_rss_news():
    all_news = []
    
    for source, url in NEWS_SOURCES.items():
        try:
            feed = feedparser.parse(url)
            
            for entry in feed.entries:
                try:
                    # استخراج اطلاعات از فید
                    title = entry.get('title', '')
                    link = entry.get('link', '')
                    summary = entry.get('summary', '')
                    content = entry.get('content', [{}])[0].get('value', '') if 'content' in entry else summary
                    published = entry.get('published', datetime.now().strftime('%Y-%m-%d %H:%M:%S'))
                    
                    # اگر محتوا HTML است، متن خالص استخراج شود
                    if content:
                        soup = BeautifulSoup(content, 'html.parser')
                        content = soup.get_text()
                    
                    # ترجمه عنوان و محتوا به فارسی اگر لازم باشد
                    title_fa = translate_to_persian(title)
                    content_fa = translate_to_persian(content)
                    
                    # دسته‌بندی خبر
                    category = categorize_news(title_fa, content_fa)
                    
                    # ایجاد شناسه یکتا برای خبر
                    news_id = hashlib.md5((title + link).encode()).hexdigest()
                    
                    # افزودن به لیست اخبار
                    news_item = {
                        'id': news_id,
                        'title': title_fa,
                        'content': content_fa,
                        'source': source,
                        'link': link,
                        'published': published,
                        'category': category,
                        'original_title': title
                    }
                    
                    all_news.append(news_item)
                except Exception as e:
                    print(f"خطا در پردازش خبر از {source}: {e}")
            
            print(f"دریافت {len(feed.entries)} خبر از {source}")
            
        except Exception as e:
            print(f"خطا در دریافت اخبار از {source}: {e}")
    
    return all_news

# استخراج اخبار از وب‌سایت‌هایی که RSS ندارند
def scrape_websites():
    all_news = []
    
    for site_name, url in WEBSITES_TO_SCRAPE.items():
        try:
            response = requests.get(url, headers={'User-Agent': 'Mozilla/5.0'})
            soup = BeautifulSoup(response.text, 'html.parser')
            
            # برای الاین پرشین
            if site_name == "alainpersian":
                # استخراج لینک‌های اخبار - این الگو باید با ساختار سایت تطبیق داده شود
                news_links = soup.select('article a.post-link')
                
                for link in news_links[:10]:  # محدود به 10 خبر برای پرهیز از کندی
                    try:
                        news_url = link['href']
                        if not news_url.startswith('http'):
                            news_url = f"https://alainpersian.com{news_url}"
                            
                        # دریافت صفحه خبر
                        news_response = requests.get(news_url, headers={'User-Agent': 'Mozilla/5.0'})
                        news_soup = BeautifulSoup(news_response.text, 'html.parser')
                        
                        # استخراج عنوان و محتوا
                        title = news_soup.select_one('h1.post-title').text.strip() if news_soup.select_one('h1.post-title') else ''
                        content_div = news_soup.select_one('div.post-content')
                        
                        if content_div:
                            content = content_div.get_text(strip=True)
                        else:
                            content = ''
                        
                        # بررسی نیاز به ترجمه
                        title_fa = translate_to_persian(title)
                        content_fa = translate_to_persian(content)
                        
                        # دسته‌بندی خبر
                        category = categorize_news(title_fa, content_fa)
                        
                        # ایجاد شناسه یکتا
                        news_id = hashlib.md5((title + news_url).encode()).hexdigest()
                        
                        news_item = {
                            'id': news_id,
                            'title': title_fa,
                            'content': content_fa[:2000],  # محدود کردن محتوا
                            'source': site_name,
                            'link': news_url,
                            'published': datetime.now().strftime('%Y-%m-%d %H:%M:%S'),
                            'category': category,
                            'original_title': title
                        }
                        
                        all_news.append(news_item)
                        time.sleep(1)  # تاخیر برای جلوگیری از بلاک شدن
                        
                    except Exception as e:
                        print(f"خطا در استخراج خبر از {news_url}: {e}")
            
        except Exception as e:
            print(f"خطا در دسترسی به {site_name}: {e}")
    
    return all_news

# ذخیره اخبار در فایل JSON
def save_news(news_list):
    existing_news = load_news()
    existing_ids = {news['id'] for news in existing_news}
    
    # افزودن اخبار جدید
    new_count = 0
    for news in news_list:
        if news['id'] not in existing_ids:
            existing_news.append(news)
            new_count += 1
    
    # ذخیره در فایل
    if new_count > 0:
        with open(os.path.join(DATA_DIR, 'news.json'), 'w', encoding='utf-8') as f:
            json.dump(existing_news, f, ensure_ascii=False, indent=2)
        print(f"{new_count} خبر جدید اضافه شد.")
    else:
        print("خبر جدیدی یافت نشد.")

# بارگذاری اخبار از فایل
def load_news():
    try:
        with open(os.path.join(DATA_DIR, 'news.json'), 'r', encoding='utf-8') as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return []

# به‌روزرسانی خودکار اخبار
def update_news_job():
    print("شروع به‌روزرسانی اخبار...")
    rss_news = fetch_rss_news()
    scraped_news = scrape_websites()
    all_news = rss_news + scraped_news
    save_news(all_news)
    print("به‌روزرسانی اخبار تکمیل شد.")

# تنظیم مسیرهای وب‌سایت
@app.route('/')
def home():
    page = request.args.get('page', 1, type=int)
    category = request.args.get('category', None)
    
    news = load_news()
    
    # مرتب‌سازی بر اساس تاریخ انتشار (نزولی)
    news.sort(key=lambda x: x.get('published', ''), reverse=True)
    
    # فیلتر بر اساس دسته‌بندی
    if category:
        news = [item for item in news if item.get('category') == category]
    
    # صفحه‌بندی
    per_page = 10
    total_pages = (len(news) - 1) // per_page + 1
    page = min(max(page, 1), total_pages)
    
    start_idx = (page - 1) * per_page
    end_idx = min(start_idx + per_page, len(news))
    displayed_news = news[start_idx:end_idx]
    
    return render_template('index.html', 
                          news=displayed_news, 
                          categories=NEWS_CATEGORIES,
                          current_category=category,
                          page=page,
                          total_pages=total_pages)

@app.route('/news/<news_id>')
def news_detail(news_id):
    news = load_news()
    news_item = next((item for item in news if item['id'] == news_id), None)
    
    if not news_item:
        return redirect(url_for('home'))
        
    return render_template('news_detail.html', news=news_item)

@app.route('/category/<category>')
def category_view(category):
    return redirect(url_for('home', category=category))

# راه‌اندازی برنامه
if __name__ == '__main__':
    # ایجاد فولدر templates
    os.makedirs('templates', exist_ok=True)
    
    # ایجاد فایل‌های قالب HTML
    with open('templates/layout.html', 'w', encoding='utf-8') as f:
        f.write('''<!DOCTYPE html>
<html dir="rtl" lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}سیستم خبری خودکار{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            font-family: 'Tahoma', 'Arial', sans-serif;
            background-color: #f8f9fa;
        }
        .navbar {
            background-color: #343a40 !important;
        }
        .news-card {
            transition: transform 0.3s;
            height: 100%;
        }
        .news-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        .category-badge {
            position: absolute;
            top: 10px;
            right: 10px;
        }
        .sidebar {
            background-color: #f8f9fa;
            border-left: 1px solid #dee2e6;
            padding: 15px;
        }
        .pagination {
            justify-content: center;
            margin-top: 20px;
        }
        .source-badge {
            position: absolute;
            top: 10px;
            left: 10px;
        }
        footer {
            margin-top: 50px;
            padding: 20px 0;
            background-color: #343a40;
            color: white;
        }
    </style>
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark mb-4">
        <div class="container">
            <a class="navbar-brand" href="/">سیستم خبری خودکار</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav me-auto">
                    <li class="nav-item">
                        <a class="nav-link" href="/">صفحه اصلی</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>

    <div class="container">
        <div class="row">
            <div class="col-md-9">
                {% block content %}{% endblock %}
            </div>
            <div class="col-md-3 sidebar">
                <h4>دسته‌بندی‌ها</h4>
                <ul class="list-group">
                    <li class="list-group-item">
                        <a href="/" class="text-decoration-none">همه اخبار</a>
                    </li>
                    {% for category in categories %}
                    <li class="list-group-item">
                        <a href="{{ url_for('category_view', category=category) }}" class="text-decoration-none">{{ category }}</a>
                    </li>
                    {% endfor %}
                </ul>
                
                <h4 class="mt-4">منابع خبری</h4>
                <ul class="list-group">
                    <li class="list-group-item">etonline</li>
                    <li class="list-group-item">hollywoodreporter</li>
                    <li class="list-group-item">independentpersian</li>
                    <li class="list-group-item">alarabiya</li>
                    <li class="list-group-item">timesofisrael</li>
                    <li class="list-group-item">radis</li>
                    <li class="list-group-item">alainpersian</li>
                </ul>
            </div>
        </div>
    </div>
    
    <footer class="text-center">
        <div class="container">
            <p>© 2025 سیستم خبری خودکار - تمامی حقوق محفوظ است.</p>
        </div>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>''')
    
    with open('templates/index.html', 'w', encoding='utf-8') as f:
        f.write('''{% extends "layout.html" %}

{% block title %}صفحه اصلی - سیستم خبری خودکار{% endblock %}

{% block content %}
    <h2 class="mb-4">
        {% if current_category %}
            اخبار دسته {{ current_category }}
        {% else %}
            آخرین اخبار
        {% endif %}
    </h2>
    
    <div class="row row-cols-1 row-cols-md-2 g-4">
        {% for item in news %}
        <div class="col">
            <div class="card news-card position-relative">
                <div class="card-body">
                    <span class="badge bg-primary category-badge">{{ item.category }}</span>
                    <span class="badge bg-secondary source-badge">{{ item.source }}</span>
                    <h5 class="card-title mt-3">{{ item.title }}</h5>
                    <p class="card-text text-muted small">{{ item.published }}</p>
                    <p class="card-text">{{ item.content|truncate(150) }}</p>
                    <a href="{{ url_for('news_detail', news_id=item.id) }}" class="btn btn-sm btn-outline-primary">ادامه مطلب</a>
                </div>
            </div>
        </div>
        {% else %}
        <div class="col-12">
            <div class="alert alert-info">
                خبری یافت نشد.
            </div>
        </div>
        {% endfor %}
    </div>
    
    {% if total_pages > 1 %}
    <nav aria-label="Page navigation">
        <ul class="pagination">
            {% if page > 1 %}
            <li class="page-item">
                <a class="page-link" href="{{ url_for('home', page=page-1, category=current_category) }}">قبلی</a>
            </li>
            {% endif %}
            
            {% for i in range(1, total_pages + 1) %}
            <li class="page-item {{ 'active' if i == page else '' }}">
                <a class="page-link" href="{{ url_for('home', page=i, category=current_category) }}">{{ i }}</a>
            </li>
            {% endfor %}
            
            {% if page < total_pages %}
            <li class="page-item">
                <a class="page-link" href="{{ url_for('home', page=page+1, category=current_category) }}">بعدی</a>
            </li>
            {% endif %}
        </ul>
    </nav>
    {% endif %}
{% endblock %}''')
    
    with open('templates/news_detail.html', 'w', encoding='utf-8') as f:
        f.write('''{% extends "layout.html" %}

{% block title %}{{ news.title }} - سیستم خبری خودکار{% endblock %}

{% block content %}
    <div class="card mb-4">
        <div class="card-header d-flex justify-content-between align-items-center">
            <span class="badge bg-primary">{{ news.category }}</span>
            <span class="badge bg-secondary">{{ news.source }}</span>
        </div>
        <div class="card-body">
            <h2 class="card-title mb-3">{{ news.title }}</h2>
            <p class="text-muted small mb-4">تاریخ انتشار: {{ news.published }}</p>
            
            <div class="card-text">
                {{ news.content|safe }}
            </div>
            
            {% if news.link %}
            <div class="mt-4">
                <a href="{{ news.link }}" target="_blank" class="btn btn-outline-primary">مشاهده منبع اصلی</a>
            </div>
            {% endif %}
        </div>
    </div>
    
    <a href="{{ url_for('home') }}" class="btn btn-secondary">بازگشت به صفحه اصلی</a>
{% endblock %}''')
    
    # ایجاد فایل JSON برای ذخیره اخبار اگر وجود ندارد
    if not os.path.exists(os.path.join(DATA_DIR, 'news.json')):
        with open(os.path.join(DATA_DIR, 'news.json'), 'w', encoding='utf-8') as f:
            json.dump([], f)
    
    # راه‌اندازی زمان‌بند برای به‌روزرسانی خودکار
    scheduler = BackgroundScheduler()
    scheduler.add_job(update_news_job, 'interval', hours=1)
    scheduler.start()
    
    try:
        # انجام اولین به‌روزرسانی
        update_news_job()
        
        # راه‌اندازی وب‌سایت
        app.run(debug=True, host='0.0.0.0', port=5000)
    except (KeyboardInterrupt, SystemExit):
        scheduler.shutdown()
