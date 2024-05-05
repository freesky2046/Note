# CollectionView Tips

## 零 目录

[TOC]

## 一 collectionview 的几种视图

- CollectionviewCell

- SupplementaryView
  - headerView
  - footerView
- DecorationView

## 二 自定义`section headerView`或`section footerview`

1. 注册：`register(_:forSupplementaryViewOfKind:withReuseIdentifier:)`
2. 实现：继承`UICollectionReusableView`
3. 创建实例：`collectionView(_:viewForSupplementaryElementOfKind:)`
4. 设定尺寸:layout 的`headerReferenceSize`或footerReferenceSize

#### 1.注册 `header`或者 `footer` 的时候，kind 怎么写

`UICollectionView.elementKindSectionHeader`

`UICollectionView.elementKindSectionFooter`

#### 2.实现的时候继承的类是：`UICollectionReusableView`

#### 3.创建实例的时候就算只创建`header` 也得写 `footer`

因为 这个在一个函数的里面返回，`if`后面必须跟着 `else`

```swift
func collectionView(_ collectionView: UICollectionView, viewForSupplementaryElementOfKind kind: String, at indexPath: IndexPath) -> UICollectionReusableView {

if kind == UICollectionView.elementKindSectionHeader {
 return collectionView.dequeueReusableCell(withReuseIdentifier: "header", for: indexPath)
 }
  else {
  return collectionView.dequeueReusableCell(withReuseIdentifier: "footer", for: indexPath)
 }

}


```

可以注册的时候直接注册为`UICollectionReusableView`类,使用系统的类可以少写一个文件

#### 4.设定尺寸的两种方式

   若全部尺寸一样且初始化的时候就可以决定可以直接使用layout的`headerReferenceSize`或`footerReferenceSize`否则要使用

```swift
   @available(iOS 6.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, referenceSizeForHeaderInSection section: Int) -> CGSize

    @available(iOS 6.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, referenceSizeForFooterInSection section: Int) -> CGSize
```

## 三 collectionview 使用 流式布局的 viewDelegate 用UICollectionViewDelegateFlowLayout

**不要使用UICollectionViewDelegate**，而是继承UICollectionViewDelegate的UICollectionViewDelegateFlowLayout

获取item尺寸，collectionviewInset ，行间距，列间距，头尺寸，尾尺寸

```swift
    @available(iOS 8.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize

    @available(iOS 6.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, insetForSectionAt section: Int) -> UIEdgeInsets

    @available(iOS 6.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, minimumLineSpacingForSectionAt section: Int) -> CGFloat

    @available(iOS 6.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, minimumInteritemSpacingForSectionAt section: Int) -> CGFloat

    @available(iOS 6.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, referenceSizeForHeaderInSection section: Int) -> CGSize

    @available(iOS 6.0, *)
    optional func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, referenceSizeForFooterInSection section: Int) -> CGSize
```



## 四 参考

> https://github.com/freesky2046/Make/tree/master/CollectionDemo/CollectionViewTips

