


<a name="readme-top"></a>





<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/semaDilthey/myNotes">
    <img src="images/Swift_logo.svg.png" alt="Logo" width="240" height="75">
  </a>

  <h3 align="center">My notes about swift</h3>
  </p>
</div>



<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#extensions">Extensions</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
# Extensions

Во время обучения набирается приличное поличество расширений, которые пригодятся в будущем как мне, так и всем ждунчикам :smile:
Решил собрать их в этом документе для удобства

<p align="right">(<a href="#readme-top">back to top</a>)</p>



## Live preview for UIKit

Расширение позволяет работать с экраном в режиме реального времени с внедрением фрейма SwiftUI. Необходимость билдинга отсутствует. Удобно при создании лэйаута. 

 1. Create an extension for UIViewController (for example: "extension+UIViewController")
 2. Ctrl + C Crtl + V code below: 

```swift
import SwiftUI

extension UIViewController {
    private struct Preview : UIViewControllerRepresentable {
    
        let viewController : UIViewController
        
        func makeUIViewController(context: Context) -> some UIViewController {
            viewController
        }
        
        func updateUIViewController(_ uiViewController: UIViewControllerType, context: Context) {
        }
    }
    
    func showPreview() -> some View {
        Preview(viewController: self).edgesIgnoringSafeArea(.all)
    }
    
}
```
 3. In any VC you are currently building, import SwiftUI and paste the following code: 

```swift
struct ViewControllerProvider : PreviewProvider {

    static var previews: some View {
        YourViewControllerName().showPreview()
    }
}
```
 4. You are flawless! 😈


<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Adding shadow for UICollectionViewCell

Полезное расширение для UICollectionViewCell, по одному вызову добавляет тень к ячейке.

Вызывать в методе cellForItemAt :
```swift
cell.addShadow()
```
Код экстеншена: 
```swift
extension UICollectionViewCell {

    func addShadow(corner: CGFloat = 10, color: UIColor = .black, radius: CGFloat = 15, offset: CGSize = CGSize(width: 0, height: 0), opacity: Float = 0.2) {
        let cell = self
        cell.contentView.layer.borderWidth = 0
        cell.contentView.layer.borderColor = UIColor.clear.cgColor
        cell.contentView.layer.masksToBounds = true
        cell.layer.shadowColor = color.cgColor
        cell.layer.shadowOffset = offset
        cell.layer.shadowRadius = radius
        cell.layer.shadowOpacity = opacity
        cell.layer.cornerRadius = 10
        cell.layer.masksToBounds = false
        cell.layer.shadowPath = UIBezierPath(roundedRect: self.bounds, cornerRadius: cell.contentView.layer.cornerRadius).cgPath
    }
}
```

Настраивать под себя как душе угодно
## Changing UIImage color

Часто получается так, что скачанный ассет имеет неподходящий цвет. Этот код позволяет менять цвет векторного изображения на любой необходимый


  ```swift
  extension UIImage {
    
    func imageWithColor(color: UIColor) -> UIImage {
        UIGraphicsBeginImageContextWithOptions(self.size, false, UIScreen.main.scale)
        let context = UIGraphicsGetCurrentContext()

        color.setFill()

        context!.translateBy(x: 0, y: self.size.height)
        context!.scaleBy(x: 1.0, y: -1.0)

        context!.setBlendMode(CGBlendMode.colorBurn)
        let rect = CGRect(x: 0, y: 0, width: self.size.width, height: self.size.height)
        context!.draw(self.cgImage!, in: rect)

        context!.setBlendMode(CGBlendMode.sourceIn)
        context!.addRect(rect)
        context!.drawPath(using: CGPathDrawingMode.fill)

        let coloredImage = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()

        return coloredImage!
    }
    
}
  ```

## Adding new UIFont to project

1. Необходимо закинуть файлы с расширением .ttf в сам проект
2. После в info.plist необходимо добавить связь
   <br />
<div align="center">
   <img width="595" alt="Снимок экрана 2023-11-09 в 22 45 31" src="https://github.com/semaDilthey/myNotes/assets/128741166/2c659e47-7239-4273-824e-9e466b8ff84a">
</div>

4. Создать файл с кодом примерно следующего содеражния. Все настройки индивидуальны
```swift
 extension UIFont {
    
    enum MarkProFontWeight: Int {
        case plain = 400
        case medium = 500
        case heavy = 800
        
        var nameFont: String {
            switch self {
            case .plain:
                return "DSLCLU+MarkPro"
            case .medium:
                return "DSLCLU+MarkPro-Medium"
            case .heavy:
                return "DSLCLU+MarkPro-Heavy"
            }
        }
    }
    
    static func markProFont(size fontSize: CGFloat, weight fontWeight: MarkProFontWeight) -> UIFont? {
        UIFont(name: fontWeight.nameFont, size: fontSize)
    }
}
```


<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- USAGE EXAMPLES -->
## SceneDelegate и AppDelegate - разбор 
AppDelegate отвечает за жизненный цикл и настройку приложения. SceneDelegate отвечает за то, что отображается на экране (Windows или Scenes), и управлять способом отображения вашего приложения.
SceneDelegate появился начиная с iOs 13.

### AppDelegate 
Возможно, вы уже знакомы с AppDelegate, это вход в приложение iOS, application(_:didFinishLaunchingWithOptions:) 
Это первая функция, вызываемая системой после запуска вашего приложения.

AppDelegate реализует библиотеку UIKit UIApplicationDelegate протокол. 



_For more examples, please refer to the [Documentation](https://russianblogs.com/article/22911460147/)_

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ROADMAP -->
## Roadmap

- [x] Add Changelog
- [x] Add back to top links
- [ ] Add Additional Templates w/ Examples
- [ ] Add "components" document to easily copy & paste sections of the readme
- [ ] Multi-language Support
    - [ ] Chinese
    - [ ] Spanish

See the [open issues](https://github.com/othneildrew/Best-README-Template/issues) for a full list of proposed features (and known issues).

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- CONTACT -->
## Contact

Your Name - [@your_twitter](https://twitter.com/your_username) - email@example.com

Project Link: [https://github.com/your_username/repo_name](https://github.com/your_username/repo_name)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

Use this space to list resources you find helpful and would like to give credit to. I've included a few of my favorites to kick things off!

* [Choose an Open Source License](https://choosealicense.com)
* [GitHub Emoji Cheat Sheet](https://www.webpagefx.com/tools/emoji-cheat-sheet)
* [Malven's Flexbox Cheatsheet](https://flexbox.malven.co/)
* [Malven's Grid Cheatsheet](https://grid.malven.co/)
* [Img Shields](https://shields.io)
* [GitHub Pages](https://pages.github.com)
* [Font Awesome](https://fontawesome.com)
* [React Icons](https://react-icons.github.io/react-icons/search)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/othneildrew/Best-README-Template.svg?style=for-the-badge
[contributors-url]: https://github.com/othneildrew/Best-README-Template/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members
[stars-shield]: https://img.shields.io/github/stars/othneildrew/Best-README-Template.svg?style=for-the-badge
[stars-url]: https://github.com/othneildrew/Best-README-Template/stargazers
[issues-shield]: https://img.shields.io/github/issues/othneildrew/Best-README-Template.svg?style=for-the-badge
[issues-url]: https://github.com/othneildrew/Best-README-Template/issues
[license-shield]: https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
[license-url]: https://github.com/othneildrew/Best-README-Template/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/othneildrew
[product-screenshot]: images/screenshot.png
[Next.js]: https://img.shields.io/badge/next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white
[Next-url]: https://nextjs.org/
[React.js]: https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB
[React-url]: https://reactjs.org/
[Vue.js]: https://img.shields.io/badge/Vue.js-35495E?style=for-the-badge&logo=vuedotjs&logoColor=4FC08D
[Vue-url]: https://vuejs.org/
[Angular.io]: https://img.shields.io/badge/Angular-DD0031?style=for-the-badge&logo=angular&logoColor=white
[Angular-url]: https://angular.io/
[Svelte.dev]: https://img.shields.io/badge/Svelte-4A4A55?style=for-the-badge&logo=svelte&logoColor=FF3E00
[Svelte-url]: https://svelte.dev/
[Laravel.com]: https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white
[Laravel-url]: https://laravel.com
[Bootstrap.com]: https://img.shields.io/badge/Bootstrap-563D7C?style=for-the-badge&logo=bootstrap&logoColor=white
[Bootstrap-url]: https://getbootstrap.com
[JQuery.com]: https://img.shields.io/badge/jQuery-0769AD?style=for-the-badge&logo=jquery&logoColor=white
[JQuery-url]: https://jquery.com 
