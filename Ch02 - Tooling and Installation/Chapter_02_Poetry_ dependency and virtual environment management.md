#Poetry ve Sanal Ortamlar (Virtual Environments)

Poetry, Python ekosistemindeki en popüler bağımlılık (dependency) ve sanal ortam yöneticilerinden biridir. Ancak, bir bağımlılık yöneticisinin ne olduğunu açıklayarak başlayalım. Python'da, bir bağımlılık yöneticisi, bir projenin dayandığı harici kütüphaneleri veya paketleri (bağımlılıkları) belirtmenize, yüklemenize, güncellenmesine ve yönetmenize olanak tanır. Örneğin, bu basit bir Poetry gereksinim dosyasıdır (requirements file) ve Python 3.11 ile requests ve numpy Python paketlerini kullanır.

```toml
[tool.poetry.dependencies]
python = "^3.11"
requests = "^2.25.1"
numpy = "^1.19.5"
[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

Yukarıdaki kodun açıklaması:
- `[tool.poetry.dependencies]` bölümünde projenin bağımlılıkları listelenir.
  - `python = "^3.11"` : Python sürümünün 3.11 veya uyumlu bir sürüm olacağını belirtir (örn: 3.11.x).
  - `requests = "^2.25.1"` ve `numpy = "^1.19.5"` : requests ve numpy kütüphanelerinin sırasıyla 2.25.1 ve 1.19.5 veya uyumlu sürümlerini belirtir.
- `[build-system]` bölümünde derleme sisteminin gereksinimleri ve arka ucu (backend) tanımlanır.
  - `requires = ["poetry-core"]` : Derleme için poetry-core'un gerekli olduğunu belirtir.
  - `build-backend = "poetry.core.masonry.api"` : Derleme arka ucunu poetry.core.masonry.api olarak tanımlar.

Poetry kullanarak bağımlılıklarınızı sabitlemek (pinning), projenizin çalışmasıyla uyumlu bağımlılıkların doğru sürümlerini her zaman yüklemenizi sağlar. Poetry, varsayılan olarak tüm gereksinimlerini repository'nizin kökünde saklanan `pyproject.toml` dosyalarında kaydeder, LLM-Engineers-Handbook deposunda görebileceğiniz gibi. Poetry kullanmanın bir diğer büyük avantajı, belirtilen Python sürümünü ve gereksinimlerini yüklediği yeni bir Python sanal ortamı oluşturmasıdır. Sanal ortam, projenizin bağımlılıklarını global Python bağımlılıklarınızdan ve diğer projelerden izole etmenize olanak tanır. Bunu yaparak, projeler arasında sürüm çakışmalarını önlersiniz. Örneğin, Proje A'nın `numpy == 1.19.5` gerektirdiğini ve Proje B'nin `numpy == 1.26.0` gerektirdiğini varsayalım. Her iki projeyi de global Python ortamında tutarsanız, bu işe yaramaz çünkü Proje B, Proje A'nın numpy kurulumunu geçersiz kılar ve Proje A'nın çalışmasını engeller. Poetry kullanarak her projeyi kendi Python ortamında kendi Python bağımlılıklarıyla izole edebilir ve herhangi bir bağımlılık çakışmasını önleyebilirsiniz.

Poetry'i buradan kurabilirsiniz: https://python-poetry.org/docs/. Kitap boyunca Poetry 1.8.3 sürümünü kullanıyoruz. Poetry kurulduktan sonra, klonlanmış LLM-Engineers-Handbook deponuza gidin ve gerekli tüm Python bağımlılıklarını yüklemek için aşağıdaki komutu çalıştırın:

```bash
poetry install --without aws
```

Bu komut, repository'nizde `pyproject.toml` ve `poetry.lock` dosyalarında listelenen tüm bağımlılıkları alır. Kurulumdan sonra, Poetry ortamınızı etkinleştirmek için terminalinizde `poetry shell` komutunu çalıştırabilir veya tüm CLI komutlarınızı aşağıdaki gibi prefixleyebilirsiniz: `poetry run <komutunuz>`.

Poetry hakkında son bir not, bağımlılık ağacının (dependency tree) tam sürümlerini `poetry.lock` dosyasına kilitlemesidir (`project.toml` dosyasına eklenen tanımlar temelinde). `pyproject.toml` dosyası sürüm aralıkları belirtebilirken (örneğin, `requests = "^2.25.1"`), `poetry.lock` dosyası tam sürümü kaydeder (örneğin, `requests = "2.25.1"`). Ayrıca, `pyproject.toml` dosyasınızda açıkça listelenmeyen alt bağımlılıkların (bağımlılıklarınızın bağımlılıkları) sürümlerini de kilitler. Tüm bağımlılıkları ve alt bağımlılıkları belirli sürümlere kilitleyerek, `poetry.lock` dosyası tüm proje kurulumlarının her paketin aynı sürümlerini kullanmasını sağlar. Bu tutarlılık, öngörülebilir davranışa yol açar ve "benim makinemde çalışıyor" sorunlarıyla karşılaşma olasılığını azaltır.

Poetry'e benzer diğer araçlar arasında sanal ortamlar oluşturmak için Venv ve Conda bulunur. Ancak, bunlar bağımlılık yönetimi seçeneğinden yoksundur. Bu nedenle, bunu Python'un varsayılan `requirements.txt` dosyaları aracılığıyla yapmalısınız, ki bunlar Poetry'nin kilit dosyalarından (lock files) daha az güçlüdür. Diğer bir seçenek de Pipenv'dir; bu, özellik açısından Poetry'e benzer, ancak daha yavaştır. Ayrıca, Rust'ta oluşturulmuş ve son derece hızlı olan uv de mevcuttur; bu, Poetry'nin yerine geçme potansiyelini taşır ve denenmesi değerlidir: https://github.com/astral-sh/uv.

Bulmacanın son parçası, tüm CLI komutlarımızı yönetmek için kullandığımız görev yürütme aracına (task execution tool) bakmaktır. 

İngilizce teknik terimlerin Türkçeleri parantez içinde verildi:
- dependency manager: bağımlılık yöneticisi
- virtual environment: sanal ortam
- dependencies: bağımlılıklar
- package: paket
- build-system: derleme sistemi
- backend: arka uç
- pinning: sabitlemek 
- repository: depo 
- CLI: Komut Satırı Arayüzü (Command Line Interface)

---

