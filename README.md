![image](https://github.com/user-attachments/assets/16c61797-3973-47bc-9a87-26d6df25ac77)


# آموزش ساده استفاده از Ktor و Kotlin Serialization در Android Compose

این آموزش برای دانشجویان تازه‌کار طراحی شده است و شما را با نحوه استفاده از Ktor و Kotlin Serialization در پروژه Android Compose آشنا می‌کند.

## 1. معرفی کوتاه Ktor و Kotlin Serialization

* **Ktor**: یک فریم‌ورک برای ارتباطات HTTP که هم در سمت سرور و هم در سمت کلاینت قابل استفاده است.
* **Kotlin Serialization**: یک کتابخانه برای سریال‌سازی (Serialization) و دی‌سریال‌سازی (Deserialization) داده‌ها.

## 2. تنظیمات پروژه

* در فایل settings.gradle.kts (ریشه پروژه) این پلاگین را اضافه کنید:

```kotlin
plugins {
    kotlin("plugin.serialization") version "1.8.21"
}
```

* در فایل build.gradle.kts (ماژول اپ) این پلاگین و وابستگی‌ها را اضافه کنید:

```kotlin
plugins {
    id("kotlinx-serialization")
}

dependencies {
    implementation("io.ktor:ktor-client-core:2.0.3")
    implementation("io.ktor:ktor-client-cio:2.0.3")
    implementation("io.ktor:ktor-client-content-negotiation:2.0.3")
    implementation("io.ktor:ktor-serialization-kotlinx-json:2.0.3")
}
```

## 3. تنظیمات فایل AndroidManifest.xml

* اطمینان حاصل کنید که دسترسی به اینترنت فعال باشد:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

* و در تگ <application>، تنظیمات مربوط به تم یا دیگر ویژگی‌ها را اضافه کنید.

## 4. مدل داده‌ها

```kotlin
import kotlinx.serialization.Serializable

@Serializable
data class Movie(
    val id: Int,
    val title: String,
    val poster: String,
    val year: String,
    val country: String,
    val runtime: String,
    val director: String,
    val actors: String,
    val plot: String,
    val imdb_id: String,
    val imdb_rating: String,
    val genres: List<String>
)

@Serializable
data class MovieResponse(
    val data: List<Movie>
)
```

## 5. استفاده از Ktor و Kotlin Serialization در Compose

```kotlin
val client = HttpClient() {
    install(ContentNegotiation) {
        json(Json { ignoreUnknownKeys = true })
    }
}

setContent {
    MontazeriSession2Theme {
        val scope = rememberCoroutineScope()
        Column(
            modifier = Modifier.fillMaxSize().padding(30.dp)
        ) {
            Spacer(modifier = Modifier.height(12.dp))
            val movies = remember { mutableStateListOf<Movie>() }
            Button(onClick = {
                scope.launch(Dispatchers.IO) {
                    movies.addAll(
                        client.get("https://moviesapi.codingfront.dev/api/v1/movies")
                            .body<MovieResponse>().data
                    )
                }
            }) {
                Text("Click for movies")
            }
            LazyColumn {
                items(movies) {
                    Text(it.title)
                }
            }
        }
    }
}
```

## 6. توضیح کامل کد (خط به خط):

1. **ایجاد HttpClient:**

   * از Ktor برای ساخت یک کلاینت HTTP استفاده می‌شود.

2. **نصب ContentNegotiation:**

   * این قابلیت به کلاینت اجازه می‌دهد که داده‌ها را به فرمت JSON دریافت و مدیریت کند.

3. **تنظیم Json:**

   * از Kotlin Serialization برای دی‌سریال‌سازی JSON استفاده می‌شود.
   * گزینه `ignoreUnknownKeys = true` به این معنی است که اگر در JSON فیلدهای ناشناخته‌ای وجود داشته باشد، نادیده گرفته می‌شوند.

4. **ساخت لیست متغیر فیلم‌ها:**

   * `mutableStateListOf<Movie>()` یک لیست متغیر ایجاد می‌کند که در Compose قابلیت مشاهده دارد.

5. **استفاده از Coroutine Scope:**

   * `rememberCoroutineScope()` یک Scope ایجاد می‌کند که به ما اجازه می‌دهد عملیات‌های همزمان (مانند درخواست شبکه) را مدیریت کنیم.

6. **دکمه درخواست:**

   * با کلیک بر روی این دکمه، درخواست HTTP به سرور ارسال می‌شود.
   * درخواست در یک Coroutine در `Dispatchers.IO` (مناسب برای عملیات شبکه) اجرا می‌شود.

7. **دی‌سریال‌سازی JSON:**

   * پاسخ دریافت شده به صورت خودکار به مدل `MovieResponse` تبدیل می‌شود.

8. **نمایش لیست فیلم‌ها:**

   * با استفاده از `LazyColumn`، هر فیلم در قالب یک آیتم نمایش داده می‌شود.
   * عنوان هر فیلم با استفاده از `Text(it.title)` نمایش داده می‌شود.
