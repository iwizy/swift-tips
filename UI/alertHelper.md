# Выводим алетр в отдельный класс

Часто стоит задача сделать универсальное и гибкое решение для, например, вызова алертов. Ниже приведено пошаговое руководство реализации базового класса и вызова влерта из другого.

Руководство не учитывает на данный момент работу с моделями и будет дополнено позже.

## Шаг 1. Создание класса алерта

Для начала следует сосздать класс, где будет находиться сам алерт и там написать следующий код:

```swift
import UIKit
class AlertPresenter {
    private weak let viewController: UIViewController?
    
    init(viewController: UIViewController?) {
        self.viewController = viewController
    }
    func showAlert() {
        let alert = UIAlertController(
            title: "Заголовок",
            message: "Тело алерта",
            preferredStyle: .alert
        ) 
        let action = UIAlertAction(title: "Ок", style: .default) { [weak self] _ in
            guard self != nil else { return }
        }
        alert.addAction(action)
        viewController?.present(alert, animated: true, completion: nil)
    }
}
```

Типичный вызов алерта, но есть несколько моменток, которе стоит уточнить:

1. Создаем приватную константу `viewController` с типом `UIViewController` и делаем опционалом и ставим ключевое слово weak для слабой ссылки. Нам это требуется для того, чтобы на конкретном вью указать, что мы показываем именно там.
2. Инициализируем наш `viewController`
3. В конце вызывем метот `present` у нашего `viewController`. Так как `present` является методом `UIViewController`.

Это все нужно для того, чтобы потом чтобы другой Вью Контроллер смог вызвать алерт, иначе можно столкнуться с ошибкой:

```
Attempt to present <UIAlertController: 0x10780ce00> on <Main.AlertPresenter: 0x105a0e680> (from <Main.AlertPresenter: 0x105a0e680>) whose view is not in the window hierarchy.
```
Здесь все готово!

## Шаг 2. Подготовка и вызов из Вью

```swift
import UIKit


final class MainViewController: UIViewController {
    @IBOutlet private weak var button: UIButton!

    private var alertBox: AlertPresenter?
    

    override func viewDidLoad() {
        super.viewDidLoad()
        alertBox = AlertPresenter(viewController: self)
    }

    
    @IBAction private func buttonClicked(_ sender: Any) {
        self.alertBox?.showAlert() 
    }
}

```

Здесь самое интересное! 

Помимо аутлета и экшена для нашей кнопки, требуется сделать несколько важных вещей:

1. Объявить переменную с опциональным типом `AlertPresenter`
2. В методе `viewDidLoad` обазятельно нужно инициализироваться, вот так: `alertBox = AlertPresenter(viewController: self)`
3. Вызвать там, где это требуется. Например, так: `self.alertBox?.showAlert()`
4. Вуаля!

## Заключение

Здесь приведена базовая практика вызова алерта из другого класса. В идеале, чтобы получилось универсальное и гибкое решение, следует учитывать еще и модель, а также действия по нажатию кнопок.

В будущем эта статья будет дополнена с учетом написанного выше.
