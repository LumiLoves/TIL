

**# upstream : 내쪽으로 fork 떠온 아이를 최신 업뎃 해주기**   
// upstream 주소셋팅  
git remote add upstream ${원래주소}  
git remote -v // 잘 추가되었나 확인  

// upstream으로 가져오기  
git fetch upstream => git merge ${가져올 브랜치명}   
또는 git pull upstream ${가져올 브랜치명}
