<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>متجر المعدات الزراعية</title>
    <!-- Bootstrap 5 RTL -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.rtl.min.css" rel="stylesheet">
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { padding-top: 0; }
        .hero-header {
            background: linear-gradient(135deg, #198754 0%, #146c43 100%);
            color: white;
            padding: 20px 0;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        }
        .hero-header .store-logo { height: 60px; width: 60px; object-fit: contain; border-radius: 50%; background: white; padding: 5px; }
        .hero-header .store-name { font-size: 2rem; font-weight: bold; }
        .whatsapp-float {
            position: fixed; bottom: 20px; left: 20px; width: 60px; height: 60px;
            background: #25D366; border-radius: 50%; display: flex; align-items: center;
            justify-content: center; box-shadow: 0 4px 12px rgba(0,0,0,0.25);
            z-index: 1000; color: white; font-size: 30px;
        }
        .admin-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 2000; }
        .admin-panel { position: relative; background: white; width: 90%; max-width: 900px; margin: 50px auto; padding: 20px; border-radius: 12px; max-height: 80vh; overflow-y: auto; }
        /* Lightbox */
        .lightbox-overlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.9); z-index: 3000; justify-content: center; align-items: center;
        }
        .lightbox-overlay.active { display: flex; }
        .lightbox-content { position: relative; max-width: 90%; max-height: 90%; text-align: center; }
        .lightbox-content img { max-width: 100%; max-height: 80vh; object-fit: contain; border-radius: 8px; }
        .lightbox-close { position: absolute; top: 10px; right: 10px; color: white; font-size: 36px; cursor: pointer; background: none; border: none; z-index: 10; }
        .lightbox-prev, .lightbox-next {
            position: absolute; top: 50%; transform: translateY(-50%);
            background: rgba(255,255,255,0.2); border: none; color: white; font-size: 32px;
            cursor: pointer; padding: 12px; border-radius: 50%; width: 60px; height: 60px;
            display: flex; align-items: center; justify-content: center; z-index: 10;
        }
        .lightbox-prev { right: 10px; }
        .lightbox-next { left: 10px; }
        .thumbnail-img { width: 80px; height: 80px; object-fit: cover; cursor: pointer; border: 2px solid transparent; border-radius: 8px; transition: border-color 0.3s; }
        .thumbnail-img:hover, .thumbnail-img.active { border-color: #198754; }
        .product-card { cursor: pointer; transition: transform 0.2s; }
        .product-card:hover { transform: translateY(-5px); }

        /* الشريط الجانبي */
        .sidebar { background: #f8f9fa; min-height: calc(100vh - 160px); padding: 20px; border-left: 1px solid #dee2e6; }
        .sidebar .list-group-item { border: none; border-radius: 8px; margin-bottom: 5px; }
        .sidebar .list-group-item.active { background-color: #198754; color: white; }
        .sidebar .list-group-item:not(.active):hover { background-color: #e9ecef; }
        .sidebar .search-box { margin-bottom: 20px; }

        @media (max-width: 767.98px) {
            .hero-header .store-name { font-size: 1.5rem; }
            .sidebar { display: none; }
        }
    </style>
</head>
<body>

<!-- ========== المستطيل العلوي الأخضر (الهيدر) ========= -->
<div class="hero-header">
    <div class="container">
        <div class="row align-items-center">
            <div class="col-auto">
                <img id="storeLogoImg" src="" alt="" class="store-logo d-none">
                <i class="fas fa-tractor fa-3x" id="defaultIcon"></i>
            </div>
            <div class="col">
                <h1 class="store-name mb-0" id="storeNameDisplay">متجر المعدات الزراعية</h1>
            </div>
            <div class="col-auto">
                <button class="btn btn-outline-light" onclick="openAdmin()" title="لوحة التحكم">
                    <i class="fas fa-cogs"></i> لوحة التحكم
                </button>
                <!-- زر الفئات للموبايل -->
                <button class="btn btn-outline-light d-md-none ms-2" type="button" data-bs-toggle="offcanvas" data-bs-target="#mobileSidebar">
                    <i class="fas fa-bars"></i> الفئات
                </button>
            </div>
        </div>
    </div>
</div>

<!-- ========== Offcanvas للموبايل ========= -->
<div class="offcanvas offcanvas-end d-md-none" tabindex="-1" id="mobileSidebar">
    <div class="offcanvas-header">
        <h5 class="offcanvas-title">الفئات</h5>
        <button type="button" class="btn-close" data-bs-dismiss="offcanvas"></button>
    </div>
    <div class="offcanvas-body">
        <div class="search-box mb-3">
            <input type="text" id="mobileSearch" class="form-control" placeholder="ابحث عن منتج..." onkeyup="handleSearch()">
        </div>
        <div class="list-group" id="mobileCategoryList"></div>
    </div>
</div>

<!-- ========== المحتوى الرئيسي (شريط جانبي + منتجات) ========= -->
<div class="container-fluid mt-4">
    <div class="row">
        <!-- الشريط الجانبي (للكمبيوتر) -->
        <div class="col-md-3 d-none d-md-block">
            <div class="sidebar">
                <div class="search-box">
                    <input type="text" id="desktopSearch" class="form-control" placeholder="ابحث عن منتج..." onkeyup="handleSearch()">
                </div>
                <div class="list-group" id="desktopCategoryList"></div>
            </div>
        </div>
        <!-- منطقة العرض -->
        <div class="col-md-9" id="main-content"></div>
    </div>
</div>

<!-- أيقونة واتساب عائمة -->
<a id="whatsappFloat" class="whatsapp-float" href="#" target="_blank" title="تواصل واتساب">
    <i class="fab fa-whatsapp"></i>
</a>

<!-- أوقات الدوام -->
<div class="container-fluid mt-3" id="workHoursBanner" style="display:none;"></div>

<!-- فوتر -->
<footer class="bg-dark text-white text-center py-3 mt-5" id="mainFooter">
    <small>© <span id="footerYear"></span> <span id="footerStoreName"></span> - جميع الحقوق محفوظة</small>
</footer>

<!-- ========== Lightbox ========= -->
<div class="lightbox-overlay" id="lightbox">
    <button class="lightbox-close" onclick="closeLightbox()">&times;</button>
    <button class="lightbox-prev" onclick="lightboxPrev()"><i class="fas fa-arrow-right"></i></button>
    <div class="lightbox-content">
        <img id="lightboxImg" src="" alt="">
    </div>
    <button class="lightbox-next" onclick="lightboxNext()"><i class="fas fa-arrow-left"></i></button>
</div>

<!-- ========== لوحة التحكم ========= -->
<div class="admin-overlay" id="adminOverlay">
    <div class="admin-panel">
        <button class="btn-close float-start" onclick="closeAdmin()"></button>
        <div id="admin-login" class="text-center p-4">
            <h3>تسجيل الدخول للوحة التحكم</h3>
            <input type="text" id="admin-user" class="form-control mb-2" placeholder="اسم المستخدم" value="admin">
            <input type="password" id="admin-pass" class="form-control mb-3" placeholder="كلمة المرور" value="admin123">
            <button class="btn btn-success" onclick="adminLogin()">دخول</button>
        </div>
        <div id="admin-dashboard" style="display:none;">
            <ul class="nav nav-tabs mb-3" id="adminTabs">
                <li class="nav-item"><a class="nav-link active" data-bs-toggle="tab" href="#tab-products">المنتجات</a></li>
                <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#tab-categories">الفئات</a></li>
                <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#tab-settings">الإعدادات</a></li>
            </ul>
            <div class="tab-content">
                <div class="tab-pane fade show active" id="tab-products">
                    <button class="btn btn-success mb-3" onclick="showAddProductModal()"><i class="fas fa-plus"></i> إضافة منتج</button>
                    <table class="table table-bordered table-hover" id="productsTable"></table>
                </div>
                <div class="tab-pane fade" id="tab-categories">
                    <button class="btn btn-success mb-3" onclick="showAddCategoryModal()"><i class="fas fa-plus"></i> إضافة فئة</button>
                    <table class="table table-bordered" id="categoriesTable"></table>
                </div>
                <div class="tab-pane fade" id="tab-settings">
                    <div class="mb-3"><label>شعار المتجر</label><input type="file" id="logoImage" class="form-control" accept="image/*"><small class="text-muted">اختر صورة مربعة (ستظهر دائرية)</small></div>
                    <div class="mb-3"><label>رقم الواتساب (مع مفتاح الدولة)</label><input type="text" id="whatsappNumber" class="form-control" placeholder="966500000000"></div>
                    <div class="mb-3"><label>اسم المتجر</label><input type="text" id="storeName" class="form-control"></div>
                    <div class="mb-3"><label>أوقات الدوام</label><textarea id="workHours" class="form-control" rows="3" placeholder="مثال: من السبت إلى الخميس 8 صباحاً - 6 مساءً"></textarea></div>
                    <button class="btn btn-success" onclick="saveSettings()">حفظ الإعدادات</button>
                </div>
            </div>
        </div>
    </div>
</div>

<!-- مودال إضافة/تعديل منتج -->
<div class="modal fade" id="productModal" tabindex="-1">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header"><h5 class="modal-title" id="productModalTitle">منتج جديد</h5></div>
            <div class="modal-body">
                <input type="hidden" id="editProductId">
                <input type="text" id="prodName" class="form-control mb-2" placeholder="اسم المنتج" required>
                <textarea id="prodDesc" class="form-control mb-2" placeholder="الوصف"></textarea>
                <input type="number" id="prodPrice" class="form-control mb-2" placeholder="السعر" step="0.01" required>
                <select id="prodCategory" class="form-select mb-2"><option value="">بدون فئة</option></select>
                <label class="form-label">صور المنتج (يمكنك اختيار عدة صور)</label>
                <input type="file" id="prodImages" class="form-control" accept="image/*" multiple>
                <small class="text-muted d-block mb-2" id="existingImagesNote"></small>
            </div>
            <div class="modal-footer"><button class="btn btn-success" onclick="saveProduct()">حفظ</button></div>
        </div>
    </div>
</div>

<!-- مودال إضافة فئة -->
<div class="modal fade" id="categoryModal" tabindex="-1">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header"><h5 class="modal-title">فئة جديدة</h5></div>
            <div class="modal-body">
                <input type="hidden" id="editCategoryId">
                <input type="text" id="catName" class="form-control mb-2" placeholder="اسم الفئة" required>
                <input type="text" id="catSlug" class="form-control mb-2" placeholder="الرابط (slug)" required>
            </div>
            <div class="modal-footer"><button class="btn btn-success" onclick="saveCategory()">حفظ</button></div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
// ==================== بيانات التطبيق ====================
const DEFAULT_CATEGORIES = [
    { id: 1, name: 'مولدات كهربائية', slug: 'generators' },
    { id: 2, name: 'مناشير بنزين', slug: 'gas-chainsaws' },
    { id: 3, name: 'معدات زراعية', slug: 'agricultural-equipment' },
    { id: 4, name: 'قطع الغيار', slug: 'spare-parts' }
];

const DEFAULT_PRODUCTS = [
    { id: 1, name: 'مولد كهرباء 5 كيلو واط', description: 'مولد بنزين هادئ مناسب للمنازل والمزارع، قدرة 5 كيلو واط، يعمل بالبنزين، مزود بعجلات لسهولة النقل.', price: 2500, images: ['https://placehold.co/400x300?text=مولد+5كيلو', 'https://placehold.co/400x300?text=مولد+5كيلو+2'], category: 1 },
    { id: 2, name: 'مولد ديزل 10 كيلو واط', description: 'مولد احترافي للمشاريع الثقيلة، قدرة 10 كيلو واط، ديزل، تشغيل كهربائي، مناسب للمصانع والمزارع الكبيرة.', price: 7800, images: ['https://placehold.co/400x300?text=مولد+ديزل'], category: 1 },
    { id: 3, name: 'منشار بنزين 20 بوصة', description: 'منشار تقطيع أشجار قوي بمحرك 62 سي سي، طول السيف 20 بوصة، نظام تشحيم أوتوماتيكي.', price: 890, images: ['https://placehold.co/400x300?text=منشار+بنزين', 'https://placehold.co/400x300?text=منشار+بنزين+2'], category: 2 },
    { id: 4, name: 'محشة أرز بنزين', description: 'ماكينة حصاد صغيرة متعددة الاستخدامات، محرك بنزين 4 شوط، خفيفة الوزن وسهلة التشغيل.', price: 3100, images: ['https://placehold.co/400x300?text=محشة+أرز'], category: 3 },
    { id: 5, name: 'حراثة يدوية ديزل', description: 'حراثة بمحرك ديزل اقتصادي، قدرة 7 حصان، نظام تروس متعدد السرعات، ممتازة للأراضي الزراعية.', price: 4600, images: ['https://placehold.co/400x300?text=حراثة', 'https://placehold.co/400x300?text=حراثة+2'], category: 3 },
    { id: 6, name: 'طقم صيانة دورية', description: 'طقم صيانة كامل يشمل شمعات احتراق، فلتر هواء، فلتر زيت، جوانات، مناسب لمختلف المعدات الزراعية.', price: 350, images: ['https://placehold.co/400x300?text=طقم+صيانة'], category: 4 }
];

let appData = {
    categories: [],
    products: [],
    settings: { 
        whatsapp: '966500000000', 
        storeName: 'متجر المعدات الزراعية',
        logo: '',
        workHours: 'من السبت إلى الخميس 8 صباحاً - 6 مساءً'
    },
    currentSection: 'home',
    currentParam: null
};

let currentProductImages = [];

// ==================== Lightbox ====================
let lightboxIndex = 0;

function openLightbox(index) {
    if (currentProductImages.length === 0) return;
    lightboxIndex = index;
    document.getElementById('lightboxImg').src = currentProductImages[index];
    document.getElementById('lightbox').classList.add('active');
}

function closeLightbox() {
    document.getElementById('lightbox').classList.remove('active');
}

function lightboxNext() {
    lightboxIndex = (lightboxIndex + 1) % currentProductImages.length;
    document.getElementById('lightboxImg').src = currentProductImages[lightboxIndex];
}

function lightboxPrev() {
    lightboxIndex = (lightboxIndex - 1 + currentProductImages.length) % currentProductImages.length;
    document.getElementById('lightboxImg').src = currentProductImages[lightboxIndex];
}

document.addEventListener('click', function(e) {
    if (e.target.id === 'lightbox') closeLightbox();
});

// ==================== التخزين والترقية ====================
function loadData() {
    let saved = localStorage.getItem('agriStoreData');
    if (saved) {
        appData = JSON.parse(saved);
        appData.products.forEach(p => {
            if (p.image && !p.images) {
                p.images = [p.image];
                delete p.image;
            }
            if (!p.images || p.images.length === 0) {
                p.images = ['https://placehold.co/400x300?text=لا+صورة'];
            }
        });
        if (!appData.settings.workHours) appData.settings.workHours = '';
        if (!appData.settings.logo) appData.settings.logo = '';
        saveData();
    } else {
        appData.categories = DEFAULT_CATEGORIES;
        appData.products = DEFAULT_PRODUCTS;
        saveData();
    }
    applySettingsToUI();
    updateWhatsappLink();
    document.title = appData.settings.storeName;
    buildCategoryLists();
}

function saveData() {
    localStorage.setItem('agriStoreData', JSON.stringify(appData));
}

function applySettingsToUI() {
    const logoImg = document.getElementById('storeLogoImg');
    const defaultIcon = document.getElementById('defaultIcon');
    const storeNameSpan = document.getElementById('storeNameDisplay');
    storeNameSpan.textContent = appData.settings.storeName;
    if (appData.settings.logo) {
        logoImg.src = appData.settings.logo;
        logoImg.classList.remove('d-none');
        defaultIcon.classList.add('d-none');
    } else {
        logoImg.classList.add('d-none');
        defaultIcon.classList.remove('d-none');
    }
    const banner = document.getElementById('workHoursBanner');
    if (appData.settings.workHours) {
        banner.style.display = 'block';
        banner.innerHTML = `<div class="alert alert-info text-center mb-0"><i class="far fa-clock"></i> أوقات الدوام: ${appData.settings.workHours}</div>`;
    } else {
        banner.style.display = 'none';
    }
    document.getElementById('footerYear').textContent = new Date().getFullYear();
    document.getElementById('footerStoreName').textContent = appData.settings.storeName;
}

function updateWhatsappLink() {
    const link = document.getElementById('whatsappFloat');
    if (link) {
        link.href = `https://wa.me/${appData.settings.whatsapp}?text=السلام عليكم، أريد الاستفسار عن منتجات المتجر`;
    }
}

function buildCategoryLists() {
    const desktopList = document.getElementById('desktopCategoryList');
    const mobileList = document.getElementById('mobileCategoryList');
    let html = `<a href="#" class="list-group-item list-group-item-action ${appData.currentSection === 'home' ? 'active' : ''}" onclick="showSection('home')">الرئيسية</a>`;
    appData.categories.forEach(cat => {
        const isActive = (appData.currentSection === 'category' && appData.currentParam === cat.slug);
        html += `<a href="#" class="list-group-item list-group-item-action ${isActive ? 'active' : ''}" onclick="showSection('category', '${cat.slug}')">${cat.name}</a>`;
    });
    if (desktopList) desktopList.innerHTML = html;
    if (mobileList) mobileList.innerHTML = html;
}

function updateActiveCategory() {
    const desktopItems = document.querySelectorAll('#desktopCategoryList .list-group-item');
    const mobileItems = document.querySelectorAll('#mobileCategoryList .list-group-item');
    function setActive(items) {
        items.forEach(item => {
            item.classList.remove('active');
            const onclick = item.getAttribute('onclick');
            if (onclick && onclick.includes(`showSection('${appData.currentSection}'`)) {
                if (appData.currentSection === 'category' && onclick.includes(`'${appData.currentParam}'`)) {
                    item.classList.add('active');
                } else if (appData.currentSection === 'home' && onclick.includes("showSection('home')")) {
                    item.classList.add('active');
                }
            }
        });
    }
    setActive(desktopItems);
    setActive(mobileItems);
}

function handleSearch() {
    const desktopSearch = document.getElementById('desktopSearch')?.value.trim().toLowerCase() || '';
    const mobileSearch = document.getElementById('mobileSearch')?.value.trim().toLowerCase() || '';
    const searchTerm = desktopSearch || mobileSearch;
    if (document.getElementById('desktopSearch')) document.getElementById('desktopSearch').value = searchTerm;
    if (document.getElementById('mobileSearch')) document.getElementById('mobileSearch').value = searchTerm;
    if (appData.currentSection === 'product') return;
    const filter = appData.currentParam || 'all';
    renderProducts(filter, searchTerm);
}

// ==================== العرض ====================
function getCategoryName(catId) {
    const cat = appData.categories.find(c => c.id == catId);
    return cat ? cat.name : 'غير مصنف';
}

function showSection(section, param) {
    appData.currentSection = section;
    appData.currentParam = param;
    if (document.getElementById('desktopSearch')) document.getElementById('desktopSearch').value = '';
    if (document.getElementById('mobileSearch')) document.getElementById('mobileSearch').value = '';
    updateActiveCategory();
    const container = document.getElementById('main-content');
    let html = '';
    if (section === 'home') {
        html = `<div id="productsList" class="row"></div>`;
        container.innerHTML = html;
        renderProducts('all');
    } else if (section === 'category') {
        html = `<h2 class="mb-3">${getCategoryNameBySlug(param)}</h2>
                <div id="productsList" class="row"></div>`;
        container.innerHTML = html;
        renderProducts(param);
    } else if (section === 'product') {
        const productId = parseInt(param);
        const p = appData.products.find(prod => prod.id === productId);
        if (!p) {
            container.innerHTML = '<p class="text-center text-danger">المنتج غير موجود.</p>';
            return;
        }
        currentProductImages = p.images;
        const catName = getCategoryName(p.category);
        const whatsappMsg = `مرحباً، أود الاستفسار عن المنتج:\nالاسم: ${p.name}\nالكود: ${p.id}\nالسعر: ${p.price} ريال`;
        const wl = `https://wa.me/${appData.settings.whatsapp}?text=${encodeURIComponent(whatsappMsg)}`;
        let thumbnailsHTML = '';
        p.images.forEach((img, idx) => {
            thumbnailsHTML += `<img src="${img}" class="thumbnail-img me-1 mb-1" onclick="openLightbox(${idx})" alt="${p.name}">`;
        });
        html = `
        <button class="btn btn-outline-success mb-3" onclick="goBack()"><i class="fas fa-arrow-right"></i> رجوع</button>
        <div class="row">
            <div class="col-md-6 mb-3 mb-md-0">
                <div class="mb-3">${thumbnailsHTML}</div>
                <img id="mainProductImage" src="${p.images[0]}" class="img-fluid rounded shadow" style="max-height:400px; cursor:pointer;" onclick="openLightbox(0)">
                <div class="text-muted small mt-2">اضغط على أي صورة للتكبير</div>
            </div>
            <div class="col-md-6">
                <h3 class="text-success">${p.name}</h3>
                <span class="badge bg-secondary mb-2">${catName}</span>
                <p class="fs-5 fw-bold text-dark">${Number(p.price).toLocaleString()} ريال</p>
                <p class="mt-3">${p.description}</p>
                <a href="${wl}" class="btn btn-success btn-lg w-100" target="_blank">
                    <i class="fab fa-whatsapp"></i> اطلب عبر الواتساب
                </a>
            </div>
        </div>`;
        container.innerHTML = html;
    }
    const offcanvas = bootstrap.Offcanvas.getInstance(document.getElementById('mobileSidebar'));
    if (offcanvas) offcanvas.hide();
}

function goBack() {
    const last = sessionStorage.getItem('lastSection') || 'home';
    const lastParam = sessionStorage.getItem('lastParam') || null;
    showSection(last, lastParam);
}

function navigateToProduct(productId) {
    sessionStorage.setItem('lastSection', appData.currentSection);
    sessionStorage.setItem('lastParam', appData.currentParam);
    showSection('product', productId);
}

function getCategoryNameBySlug(slug) {
    const cat = appData.categories.find(c => c.slug === slug);
    return cat ? cat.name : '';
}

function renderProducts(filter, searchTerm = null) {
    let products = [...appData.products];
    const spareCat = appData.categories.find(c => c.slug === 'spare-parts');
    const sparePartsId = spareCat ? spareCat.id : null;

    if (filter === 'all') {
        if (sparePartsId) {
            products = products.filter(p => p.category != sparePartsId);
        }
    } else {
        if (!isNaN(filter)) {
            products = products.filter(p => p.category == filter);
        } else {
            const cat = appData.categories.find(c => c.slug === filter);
            if (cat) {
                products = products.filter(p => p.category == cat.id);
            }
        }
    }

    if (searchTerm === null) {
        const d = document.getElementById('desktopSearch')?.value.trim().toLowerCase();
        const m = document.getElementById('mobileSearch')?.value.trim().toLowerCase();
        searchTerm = d || m;
    }
    if (searchTerm) {
        products = products.filter(p => p.name.toLowerCase().includes(searchTerm));
    }

    const container = document.getElementById('productsList');
    if (!container) return;
    let html = '';
    products.forEach(p => {
        const catName = getCategoryName(p.category);
        const firstImg = p.images[0] || 'https://placehold.co/400x300?text=لا+صورة';
        html += `
        <div class="col-lg-3 col-md-4 col-sm-6 mb-4">
            <div class="card h-100 shadow-sm border-success product-card" onclick="navigateToProduct(${p.id})">
                <img src="${firstImg}" class="card-img-top" alt="${p.name}">
                <div class="card-body d-flex flex-column">
                    <h5 class="card-title text-success">${p.name}</h5>
                    <p class="card-text small flex-grow-1">${p.description.substring(0,80)}...</p>
                    <div class="d-flex justify-content-between align-items-center mt-2">
                        <span class="fw-bold text-dark">${Number(p.price).toLocaleString()} ريال</span>
                        <span class="badge bg-secondary">${catName}</span>
                    </div>
                </div>
            </div>
        </div>`;
    });
    container.innerHTML = html || '<p class="text-center">لا توجد منتجات.</p>';
}

// ==================== لوحة التحكم (بدون تغيير) ====================
function openAdmin() { document.getElementById('adminOverlay').style.display = 'block'; }
function closeAdmin() { document.getElementById('adminOverlay').style.display = 'none'; }

function adminLogin() {
    const user = document.getElementById('admin-user').value;
    const pass = document.getElementById('admin-pass').value;
    if (user === 'admin' && pass === 'admin123') {
        document.getElementById('admin-login').style.display = 'none';
        document.getElementById('admin-dashboard').style.display = 'block';
        refreshAdminTables();
        document.getElementById('whatsappNumber').value = appData.settings.whatsapp;
        document.getElementById('storeName').value = appData.settings.storeName;
        document.getElementById('workHours').value = appData.settings.workHours || '';
    } else {
        alert('بيانات الدخول غير صحيحة');
    }
}

function refreshAdminTables() {
    let prodHtml = '';
    appData.products.forEach(p => {
        const firstImage = (p.images && p.images.length > 0) ? p.images[0] : 'https://placehold.co/50?text=لا+صورة';
        prodHtml += `<tr>
            <td><img src="${firstImage}" width="50" height="50" style="object-fit:cover;"></td>
            <td>${p.name}</td>
            <td>${getCategoryName(p.category)}</td>
            <td>${p.price} ريال</td>
            <td>
                <button class="btn btn-sm btn-warning" onclick="editProduct(${p.id})"><i class="fas fa-edit"></i></button>
                <button class="btn btn-sm btn-danger" onclick="deleteProduct(${p.id})"><i class="fas fa-trash"></i></button>
            </td>
        </tr>`;
    });
    document.getElementById('productsTable').innerHTML = prodHtml;
    let catHtml = '';
    appData.categories.forEach(c => {
        catHtml += `<tr>
            <td>${c.name}</td>
            <td>${c.slug}</td>
            <td>
                <button class="btn btn-sm btn-warning" onclick="editCategory(${c.id})"><i class="fas fa-edit"></i></button>
                <button class="btn btn-sm btn-danger" onclick="deleteCategory(${c.id})"><i class="fas fa-trash"></i></button>
            </td>
        </tr>`;
    });
    document.getElementById('categoriesTable').innerHTML = catHtml;
    const select = document.getElementById('prodCategory');
    select.innerHTML = '<option value="">بدون فئة</option>';
    appData.categories.forEach(c => {
        select.innerHTML += `<option value="${c.id}">${c.name}</option>`;
    });
}

function deleteProduct(id) {
    if (confirm('حذف المنتج؟')) {
        appData.products = appData.products.filter(p => p.id != id);
        saveData();
        refreshAdminTables();
        showSection('home');
    }
}

function editProduct(id) {
    const p = appData.products.find(p => p.id == id);
    if (!p) return;
    document.getElementById('editProductId').value = p.id;
    document.getElementById('prodName').value = p.name;
    document.getElementById('prodDesc').value = p.description;
    document.getElementById('prodPrice').value = p.price;
    document.getElementById('prodCategory').value = p.category || '';
    document.getElementById('productModalTitle').textContent = 'تعديل منتج';
    const note = document.getElementById('existingImagesNote');
    if (p.images && p.images.length > 0) {
        note.textContent = `يوجد ${p.images.length} صورة حالياً. اختر صوراً جديدة لاستبدالها.`;
    } else {
        note.textContent = '';
    }
    document.getElementById('prodImages').value = '';
    new bootstrap.Modal(document.getElementById('productModal')).show();
}

function showAddProductModal() {
    document.getElementById('editProductId').value = '';
    document.getElementById('prodName').value = '';
    document.getElementById('prodDesc').value = '';
    document.getElementById('prodPrice').value = '';
    document.getElementById('prodCategory').value = '';
    document.getElementById('productModalTitle').textContent = 'منتج جديد';
    document.getElementById('existingImagesNote').textContent = '';
    document.getElementById('prodImages').value = '';
    new bootstrap.Modal(document.getElementById('productModal')).show();
}

async function saveProduct() {
    const id = document.getElementById('editProductId').value;
    const name = document.getElementById('prodName').value.trim();
    const desc = document.getElementById('prodDesc').value.trim();
    const price = parseFloat(document.getElementById('prodPrice').value);
    const cat = document.getElementById('prodCategory').value;
    const files = document.getElementById('prodImages').files;
    if (!name || isNaN(price)) return alert('الاسم والسعر مطلوبان');
    let images = [];
    if (files && files.length > 0) {
        for (let i = 0; i < files.length; i++) {
            const base64 = await fileToBase64(files[i]);
            if (base64) images.push(base64);
        }
        if (images.length === 0) {
            alert('فشل في قراءة الصور');
            return;
        }
    } else {
        if (id) {
            const existing = appData.products.find(p => p.id == id);
            if (existing && existing.images) images = existing.images;
        }
        if (images.length === 0) {
            images = ['https://placehold.co/400x300?text=' + encodeURIComponent(name)];
        }
    }
    if (id) {
        const p = appData.products.find(p => p.id == id);
        if (p) {
            p.name = name; p.description = desc; p.price = price;
            p.category = cat ? parseInt(cat) : null;
            p.images = images;
        }
    } else {
        const newId = appData.products.length ? Math.max(...appData.products.map(p => p.id)) + 1 : 1;
        appData.products.push({
            id: newId, name, description: desc, price,
            category: cat ? parseInt(cat) : null,
            images: images
        });
    }
    saveData();
    refreshAdminTables();
    buildCategoryLists();
    showSection('home');
    bootstrap.Modal.getInstance(document.getElementById('productModal')).hide();
}

function fileToBase64(file) {
    return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = (e) => resolve(e.target.result);
        reader.onerror = () => resolve(null);
        reader.readAsDataURL(file);
    });
}

function editCategory(id) {
    const c = appData.categories.find(c => c.id == id);
    if (!c) return;
    document.getElementById('editCategoryId').value = c.id;
    document.getElementById('catName').value = c.name;
    document.getElementById('catSlug').value = c.slug;
    new bootstrap.Modal(document.getElementById('categoryModal')).show();
}

function showAddCategoryModal() {
    document.getElementById('editCategoryId').value = '';
    document.getElementById('catName').value = '';
    document.getElementById('catSlug').value = '';
    new bootstrap.Modal(document.getElementById('categoryModal')).show();
}

function saveCategory() {
    const id = document.getElementById('editCategoryId').value;
    const name = document.getElementById('catName').value.trim();
    const slug = document.getElementById('catSlug').value.trim();
    if (!name || !slug) return alert('كل الحقول مطلوبة');
    if (id) {
        const c = appData.categories.find(c => c.id == id);
        if (c) { c.name = name; c.slug = slug; }
    } else {
        const newId = appData.categories.length ? Math.max(...appData.categories.map(c => c.id)) + 1 : 1;
        appData.categories.push({ id: newId, name, slug });
    }
    saveData();
    refreshAdminTables();
    buildCategoryLists();
    bootstrap.Modal.getInstance(document.getElementById('categoryModal')).hide();
}

function deleteCategory(id) {
    if (confirm('حذف الفئة؟ ستفقد المنتجات المرتبطة تصنيفها.')) {
        appData.categories = appData.categories.filter(c => c.id != id);
        appData.products.forEach(p => { if (p.category == id) p.category = null; });
        saveData();
        refreshAdminTables();
        buildCategoryLists();
        showSection('home');
    }
}

function saveSettings() {
    appData.settings.whatsapp = document.getElementById('whatsappNumber').value.trim();
    appData.settings.storeName = document.getElementById('storeName').value.trim();
    appData.settings.workHours = document.getElementById('workHours').value.trim();
    const logoFile = document.getElementById('logoImage').files[0];
    if (logoFile) {
        const reader = new FileReader();
        reader.onload = function(e) {
            appData.settings.logo = e.target.result;
            finishSaving();
        };
        reader.readAsDataURL(logoFile);
    } else {
        finishSaving();
    }
}

function finishSaving() {
    saveData();
    applySettingsToUI();
    updateWhatsappLink();
    document.title = appData.settings.storeName;
    alert('تم حفظ الإعدادات');
    document.getElementById('logoImage').value = '';
}

// ==================== بدء التشغيل ====================
window.onload = function() {
    loadData();
    showSection('home');
};
</script>
</body>
</html>
