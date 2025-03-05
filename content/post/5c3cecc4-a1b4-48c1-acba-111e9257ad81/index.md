---
date: 2018-04-26
publish: true
slug: 6390a1f9-31dc-441e-9d5a-0a727cf9a6c9
tags:
- CS/CAndCpp
title: CScope.md
---
# c的變數可見域

半夜隔壁房再吵，睡不著寫第一篇教學幹話。其實是自己金魚腦，要筆記。

### 區域變數

區域變數(幹在函數第一層)，你寫在函數裡的那一種(main也算)，只有宣告下面的看的見，如果再”{}”裡，就裡面看的到。

```c
int main(){ 
 int x;  
 for(int i;;){  
  int y;  
 }  
}
```

x main內全可見，i,y for內可見。

### 全域變數

全域變數，沒extern(就那個讓你在c裡寫其他語言的關鍵字)就你該張檔看的到，有的話include會汙染，  
通常拿來讓函式回傳不只一個質。

```c
int g;  
int func(){  
	++g; 
	return 0;  
}  
int main(){ 
	return g?func():2; 
}
```

g全域可見  
static，修飾字，加給全域變數時是幹話，加給函式時會在編譯時分配通常在data區，離開函式時值不話消失，  
故不能遞迴，初始質只會處裡一次，可以直接給在函式裡。

```c
int func(){
	static int g=0;  
	return ++g; 
}  
int main(){ 
	int x=func();  
	x=func();  
	x=func(); 
}
```

x為1，2，3

全域，static不給初始質是0，區域看你電腦心情。  
同名變數部分，會先遮蔽全域，至於涵式內同名，你去死。

先暫時打在fb，等到天荒地老我把我網站做好再搬過去，程式多沒測，通常也沒什麼意義。

> 現在這文應該已經在我部落格了應該拉
