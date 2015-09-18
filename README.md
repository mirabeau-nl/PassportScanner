PassportScanner
=============

[![Issues](https://img.shields.io/github/issues-raw/evermeer/PassportScanner.svg?style=flat)](https://github.com/evermeer/PassportScanner/issues)
[![Stars](https://img.shields.io/github/stars/evermeer/PassportScanner.svg?style=flat)](https://github.com/evermeer/PassportScanner/stargazers)
[![Version](https://img.shields.io/cocoapods/v/PassportScanner.svg?style=flat)](http://cocoadocs.org/docsets/PassportScanner)
[![License](https://img.shields.io/cocoapods/l/PassportScanner.svg?style=flat)](http://cocoadocs.org/docsets/PassportScanner)
[![Platform](https://img.shields.io/cocoapods/p/PassportScanner.svg?style=flat)](http://cocoadocs.org/docsets/PassportScanner)
[![Documentation](https://img.shields.io/badge/documented-100%-brightgreen.svg?style=flat)](http://cocoadocs.org/docsets/PassportScanner)

[![Git](https://img.shields.io/badge/GitHub-evermeer-blue.svg?style=flat)](https://github.com/evermeer)
[![Twitter](https://img.shields.io/badge/twitter-@evermeer-blue.svg?style=flat)](http://twitter.com/evermeer)
[![LinkedIn](https://img.shields.io/badge/linkedin-Edwin Vermeer-blue.svg?style=flat)](http://nl.linkedin.com/in/evermeer/en)
[![eMail](https://img.shields.io/badge/email-edwin@mirabeau.nl-blue.svg?style=flat)](mailto:edwin@mirabeau.nl?SUBJECT=About PassportScanner)

## What is this
With PassportScanner you can use your camera to scan the [MRZ code](http://en.wikipedia.org/wiki/Machine-readable_passport) of a passport. It will extract all data like firstname, lastname, passport number, nationality, date of birth, expiration date and personal numer.

**IMPORTANT NOTICE:** SCANNING IDENTITY DOCUMENTS IS IN MOST CASES RESTRICTED BY LAW. OBSERVE THE APPLICABLE LAWS USING THIS TOOL. THE COPYRIGHT HOLDER IS NOT IN ANY WAY LIABLE FOR UNLAWFUL USAGE OF THIS TOOL.

PassportScanner is trying to optimize OCR results by first performing some graphic filters. The exposure filter is dynamic. This means that if the image is dark it tries to light it up and visa versa. As you can see in the demo animation below you will be able to scan a passport about once every 3 seconds.

###### wait a moment until the .gif below is downloaded...
![animated](https://github.com/evermeer/PassportScanner/blob/master/ezgif.gif?raw=true)


## Building the PassportScanner demo

The current version is tested with Xcode 7 beta 6 and Xcode 7.1 beta 1. (Swift 2)

1) Clone the repo to a working directory

2) [CocoaPods](http://cocoapods.org) is used to manage dependencies. Pods are setup easily and are distributed via a ruby gem. Follow the simple instructions on the website to setup. After setup, run the following command from the toplevel directory of PassportScanner to download the dependencies:

```sh
pod install
```

3) Open the `PassportScanner.xcworkspace` in Xcode.

4) Build and Run the app.

## External components for the demo
PassportScanner is using the following components which can be installed using [CocoaPods](http://cocoapods.org).

- [TesseractOCRiOS](https://github.com/gali8/Tesseract-OCR-iOS) Tesseract OCR iOS is a Framework for iOS7+.
- [GPUImage](https://github.com/BradLarson/GPUImage) An open source iOS framework for GPU-based image and video processing
- [UIImage-Resize](https://github.com/AliSoftware/UIImage-Resize) Category to add some resizing methods to the UIImage class, to resize it to a given CGSize — or fit in a CGSize keeping aspect ratio

## Using PassportScanner in your own App 

'PassportScanner' is now available through the dependency manager [CocoaPods](http://cocoapods.org). 
You do have to use cocoapods version 0.36. At this moment this can be installed by executing:

```
[sudo] gem install cocoapods
```

If you have installed cocoapods version 0.36 or later, then you can just add PassportScanner to your workspace by making sure the following lines are in your Podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'
use_frameworks!
pod "PassportScanner"
```

Version 0.36 of cocoapods will make a dynamic framework of all the pods that you use. Because of that it's only supported in iOS 8.0 or later. When using a framework, you also have to add an import at the top of your swift file like this:

```
import PassportScanner
```

If you want support for older versions than iOS 8.0, then you can also just copy the Pod folder containing the 2 classes MRZ and PassportScannerController to your app. Also add the above mentioned external components to your podfile.

When PassportScanner is added to your project, then copy the PassportScanner view from the Main.storyboard of the demo app to your own storyboard. After that you can customize that view. Also copy the MyScanViewController.sift to your own project. See the ViewConroller.swift to see how you can initiate the view and get the MRZ data

also copy over the tessdata folder to our app. That folder contains the trained data for tesseract (The OCR engine)

Here is sample code for a PassportScannerController:


```
protocol ProcessMRZ {
    func processMRZ(mrz:MRZ)
}

class MyScanViewController: PassportScannerController {

    var delegate: ProcessMRZ?

    // the .StartScan and .EndScan are IBOutlets and can be linked to your own buttons

    /**
        For now just start scanning the moment this view is loaded
    */
    override func viewDidLoad() {
        super.viewDidLoad()
        self.debug = true  // So that we can see what's going on (scan text and quality indicator)   
        self.accuracy = 1  // 1 = all checksums shoould pass
        self.StartScan(self)
    }

    override func succesfullScan(mrz: MRZ) {
        print("mrz: {\(mrz.description)\n}")
        delegate?.processMRZ(mrz)
        self.dismissViewControllerAnimated(true, completion: nil)
    }

    override func abbortScan() {
        self.dismissViewControllerAnimated(true, completion: nil)
    }
}
```
Then in your own code you can access this PassportScannerController and process the MRZ data with something like this:

```
class ViewController: UIViewController, ProcessMRZ {

    @IBOutlet weak var mrzLabel: UILabel!

    override func supportedInterfaceOrientations() -> UIInterfaceOrientationMask {
        return UIInterfaceOrientationMask.Portrait
    }

    override func prefersStatusBarHidden() -> Bool {
        return true
    }

    @IBAction func StartScan(sender: AnyObject) {
        let storyboard: UIStoryboard = UIStoryboard(name: "Main", bundle: nil)
        let scanVC: MyScanViewController = storyboard.instantiateViewControllerWithIdentifier("PassportScanner") as! MyScanViewController
        scanVC.delegate = self
        self.presentViewController(scanVC, animated: true, completion: nil)
    }

    func processMRZ(mrz:MRZ) {
        self.mrzLabel.text = mrz.description
        mrzLabel.sizeToFit()
    }
}
```

## License

PassportScanner is available under the MIT 3 license. See the LICENSE file for more info.



