

**# upstream : 내쪽으로 fork 떠온 아이를 최신 업뎃 해주기**   
// upstream 주소셋팅  
git remote add upstream ${원래주소}
git remote -v // 잘 추가되었나 확인  

// upstream으로 가져오기  
git fetch upstream => git merge ${가져올 브랜치명}   
또는 git pull upstream ${가져올 브랜치명}


**# tag로 브랜치 따기**
git checkout ${브랜치 만드 기준이 되는 tag 이름} ==> 이때는 HEAD가 분리된 상태. 커밋을 유지할 수 있는 브랜치로 셋팅된 상태가 아님.
git checkout -b ${새 branch 이름} ==> 해당 커밋 위치 기준으로 브랜치 생성

