```javaScript
//класс Product, для создания экземпляров товаров в массиве productsInCatalog
      class Product {
        constructor(
          article,
          nameProduct,
          description,
          priceWithoutDiscount,
          priceWithDiscount
        ) {
          this.article = article;
          this.nameProduct = nameProduct;
          this.description = description;
          this.priceWithoutDiscount = priceWithoutDiscount;
          this.calculateDiscountedPrice();
          this.priceWithDiscount = this.calculateDiscountedPrice();
        }

        toString() {
          return `${this.article} ${this.nameProduct} (${this.description})\nцена со скидкой - ${this.priceWithDiscount},\nцена без скидки - ${this.priceWithoutDiscount}.\n\n`;
        }

//метод calculateDiscountedPrice() для расчета скидки на определенную группу товаров(указываем префикс группы товаров(первую цифру артикула)) ;)
        calculateDiscountedPrice() {
          if (this.article[0] === "1") {
            return Math.round(
              this.priceWithoutDiscount - (this.priceWithoutDiscount * 10) / 100
            );
          }
          return this.priceWithoutDiscount;
        }
      }

//class Catalog для создания экземпляра разела "Каталог"
      class Catalog {
        constructor(productsInCatalog) {
          this.productsInCatalog = productsInCatalog;
        }

//метод getItems() выводит все товары из массива productsInCatalog и предлагает ввести артикул товаров для добавления в один из разделов
        getItems() {
          while (true) {
            let input = prompt(
              `Вы находитесь в разделе "Каталог":\n\n${this.displayProducts()}\nВведите артикул товара для добавления в Избранное или Корзину:`
            );

            if (input === null) {
              break;
            }

//проверка ввода артикула товара для добавления в один из разделовов
            let isValidInput = false;

            for (let i = 0; i < this.productsInCatalog.length; i++) {
              let product = this.productsInCatalog[i];
              if (product.article === input) {
                isValidInput = true;
                break;
              }
            }
            if (!isValidInput) {
              alert(
                "Некорректный ввод артикула товара. Пожалуйста, введите правильный артикул товара."
              );
              continue;
            }

//при успешной проверки вызывается метод addInСategory()
            favorites.addInСategory(input);
            break;
          }
        }

//метод displayProducts() для отображения товаров в каталоге полученный из массива productsInCatalog
        displayProducts() {
          let productsList = "";
          for (let i = 0; i < this.productsInCatalog.length; i++) {
            let product = this.productsInCatalog[i];
            productsList += product.toString();
          }
          return productsList;
        }
      }

//class Favorites для создания экземпляра раздела "Избранное"
      class Favorites extends Catalog {
        constructor(productsInCatalog) {
          super(productsInCatalog);
          this.ArrayFavorites = [];
        }

//метод addInСategory() показывает новое модально окно, где пользователь выбирает в какой раздел добавить товар
        addInСategory(selectedArticle) {
          for (let product of this.productsInCatalog) {
            if (product.article === selectedArticle) {
              while (true) {
                let inputCategories = prompt(
                  "В какой раздел нужно добавить товар?\n1. Избранное\n2. Корзина"
                );

                if (inputCategories === null) {
                  alert("Вы отказались от добавления товара");
                  this.getItems();
                  break;
                }

                if (inputCategories === "1") {
                  favorites.ArrayFavorites.push(product);
                  this.getItems();
                  break;
                } else if (inputCategories === "2") {
                  cart.ArrayCart.push(product);
                  this.getItems();
                  break;
                } else {
                  alert(
                    "Некорректный ввод раздела. Пожалуйста, введите 1 или 2."
                  );
                }
              }
            }
          }
        }

//метод removeFavorites() для удаления товаров из раздела "Избранное"
        removeFavorites(selectedArticle) {
          this.ArrayFavorites = this.ArrayFavorites.filter(function (product) {
            return product.article !== selectedArticle;
          });

//Вызываем метод getItemsFavorites() для отображения обновленных товаров в разделе "Избранное"
          this.getItemsFavorites();
        }

//метод getItemsFavorites() отображает добавленные товары в раздел "Избранное" и предлагает ввести артикул товара для удаления
        getItemsFavorites() {
          let items = "";
          for (let product of this.ArrayFavorites) {
            items += product.toString() + "\n";
          }

          console.table(this.ArrayFavorites);

          let input;
          while (true) {
            input = prompt(
              `Вы находитесь в разделе "Избранное":\n\n${items}\nВведите артикул товара для удаления:`
            );

//проверка ввода артикула товара для удаления
            if (input === null || input.trim() === "") {
              break;
            } else {
              let isValidInput = false;
              for (let product of this.ArrayFavorites) {
                if (product.article === input) {
                  isValidInput = true;
                  break;
                }
              }

              if (!isValidInput) {
                alert(
                  "Некорректный артикул товара. Пожалуйста, введите правильный артикул товара."
                );
              } else {
                break;
              }
            }
          }

          if (input === null || input.trim() === "") {
            return;
          }

//при успешной проверке вызывает метод removeFavorites() для удаления товара
          this.removeFavorites(input);
        }
      }
//class Cart для создания экземляра "Корзина"
      class Cart extends Favorites {
        constructor(productsInCatalog) {
          super(productsInCatalog);
          this.ArrayCart = [];
        }

//метод removeCart() для удаления товаров из раздела "Корзина"
        removeCart(cartArticle) {
          this.ArrayCart = this.ArrayCart.filter(function (product) {
            return product.article !== cartArticle;
          });
        }

//метод calculatAmount() для расчета общей стоимости товаров в корзине без скидки
        calculatAmount() {
          let sumWithoutDiscount = 0;
          for (let product of this.ArrayCart) {
            sumWithoutDiscount += product.priceWithoutDiscount;
          }
          return sumWithoutDiscount;
        }

//метод calculatDiscount() для расчета общей стоимости товаров в корзине со скидки
        calculatDiscount() {
          let sumWithDiscount = 0;
          for (let product of this.ArrayCart) {
            sumWithDiscount += product.priceWithDiscount;
          }
          return sumWithDiscount;
        }

//метод getItemsCart() отображает товары добавленные в "Корзину", а так же сумму товаров в корзине без скидки и со скидкой,
//и предлагает ввести артикул товра для удаления из раздела
        getItemsCart() {
          while (true) {
            let items = this.ArrayCart.join("\n")

// объект arrayTotalSum служит для отображения в консоле для проверки тестовых данных
            let arrayTotalSum = {
              totalPrice: this.calculatAmount(),
              totalPriceDiscount: this.calculatDiscount(),
              totalDiscount: this.calculatAmount() - this.calculatDiscount(),
            };
            console.table(arrayTotalSum);
            console.table(this.ArrayCart);

            let input;
            while (true) {
              input = prompt(
                `Вы находитесь в разделе "Корзина":\n\n${items}\nОбщая цена за весь товар: ${cart.calculatAmount()}\nОбщая цена за весь товар со скидкой: ${cart.calculatDiscount()}\nВаша скидка составила: ${
                  arrayTotalSum.totalDiscount
                }\n\nВведите артикул товара для удаления:`
              );

// проверка ввода артикула для удаления товаров из раздела
              if (input === null || input.trim() === "") {
                return; 
              } else {
                let isValidInput = false;
                for (let product of this.ArrayCart) {
                  if (product.article === input) {
                    isValidInput = true;
                    break;
                  }
                }

                if (!isValidInput) {
                  alert(
                    "Некорректный артикул товара. Пожалуйста, введите правильный артикул товара."
                  );
                } else {
                  break;
                }
              }
            }

// при успешной проверке вызывается метод removeCart()
            this.removeCart(input);
          }
        }
      }

//массив productsInCatalog содержит экземпляры товаров класса Product
      let productsInCatalog = [
        new Product(
          "101",
          "SF110D-05-EU Cisco",
          `Неуправляемый сетевой коммутатор, 5xFE RJ-45`,
          3767
        ),
        new Product(
          "102",
          "ES‑24‑LITE Ubiquiti EdgeSwitch",
          `Управляемый сетевой коммутатор, 24xGE RJ-45`,
          19574
        ),
        new Product(
          "103",
          "SG350-28P-K9 Cisco PoE",
          `Сетевой коммутатор, 24xGE RJ-45`,
          44836
        ),
        new Product(
          "201",
          "Zyxel LTE3301-M209",
          `Внутренний LTE роутер, 1xmicroSIM, 4xFE, WIFI 802.11b/g/n`,
          11042
        ),
        new Product(
          "202",
          "Keenetic Ultra KN-1810",
          `Интернет-центр WAN 1xGE RJ-45, LAN 5xGE RJ-45, 802.11n, 2xUSB`,
          10552
        ),
        new Product(
          "302",
          "MGBT1 Cisco",
          `Модуль SFP 1000BASE-T Category 5e UTP до 100 м`,
          8960
        ),
        new Product(
          "303",
          "MGBSX1 Cisco",
          `Модуль SFP 1000BASE-SX multimode 850 nm до 550 м`,
          9855
        ),
      ];

//создем экземпляры разделов "Каталог", "Избранное" и "Корзина"
      let catalog = new Catalog(productsInCatalog);
      let favorites = new Favorites(productsInCatalog);
      let cart = new Cart(productsInCatalog);

//запускаем приложение ;)
      while (true) {
        let input =
          prompt(`Вы находитесь в главном меню приложения, для перехода в нужный раздел введите его номер, для выхода из приложения введите 4:
1. Каталог
2. Избранное
3. Корзина
4. Выход`);

        if (
          input === null ||
          input.trim() === "" ||
          isNaN(Number(input)) ||
          Number(input) < 1 ||
          Number(input) > 4
        ) {
          alert(
            "Неверный ввод номера раздела.\nВведите числовое значение от 1 до 3 в зависимости от необходимого раздела или введите 4 для выхода"
          );
        }

        if (Number(input) === 1) {
          catalog.getItems();
        } else if (Number(input) === 2) {
          favorites.getItemsFavorites();
        } else if (Number(input) === 3) {
          cart.getItemsCart();
        } else if (Number(input) === 4) {
          break;
        }
      }

```