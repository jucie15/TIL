## 숨김 파일 표시하기

```
defaults write com.apple.finder AppleShowAllFiles -bool true
killall Finder
```

## 다시 숨기기

```
defaults write com.apple.finder AppleShowAllFiles -bool false
killall Finder
```