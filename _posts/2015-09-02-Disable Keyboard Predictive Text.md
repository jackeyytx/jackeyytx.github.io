---
date: 2015-08-02 06:52:15+00:00
layout: post
title: 关闭系统键盘的联想输入

tag:
- iOS基础

---




### Disable Keyboard Predictive Text

    
    textField.autocorrectionType = Yes;
    textField.autocorrectionType = No;
    
    myTextView.autocorrectionType = UITextAutocorrectionTypeNo;
    myTextView.autocorrectionType = UITextAutocorrectionTypeYes;
    

放在 becomeFirstResponder前面或者直接在-viewDidLoad里面设置即可