# family-card-game-1-about-personal-growth
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>家庭表达盲盒｜双语抽卡游戏</title>
    <style>
        * {
            box-sizing: border-box;
            font-family: system-ui, sans-serif;
        }
        body {
            max-width:700px;
            margin:40px auto;
            padding:0 20px;
            background:#f7f9fc;
        }
        .flip-card-container{
            perspective:1000px;
        }
        .flip-card {
            width:100%;
            min-height:340px;
            position:relative;
            transform-style:preserve-3d;
            transition: transform 0.7s ease;
        }
        .flip-card.flipped{
            transform: rotateY(180deg);
        }
        .card-front, .card-back{
            position:absolute;
            width:100%;
            height:100%;
            backface-visibility:hidden;
            border-radius:18px;
            box-shadow: 0 4px 14px #00000018;
            padding:30px;
        }
        .card-front{
            background:#3377dd;
            color:#fff;
            display:flex;
            align-items:center;
            justify-content:center;
            font-size:24px;
        }
        .card-back{
            background:#fff;
            transform: rotateY(180deg);
        }
        .num-tag{
            color:#3377dd;
            font-size:20px;
            font-weight:bold;
        }
        .cn{
            font-size: 20px;
            margin:16px 0;
            line-height:1.6;
        }
        .en{
            color:#444;
            line-height:1.5;
        }
        .tip{
            margin-top:14px;
            color:#777;
        }
        .info-text{
            margin:12px 0;
            color:#555;
        }
        button{
            padding:14px 24px;
            border:none;
            border-radius:12px;
            background:#3377dd;
            color:white;
            font-size:17px;
            cursor:pointer;
            margin:10px 10px 0 0;
        }
        button#reset{
            background:#888;
        }
        button:disabled{
            background:#cccccc;
            cursor:not-allowed;
        }
    </style>
</head>
<body>
    <h2>🎴 家庭表达盲盒 抽卡游戏</h2>
    <div class="info-text">已抽取：<span id="usedCount">0</span> / 100</div>
    <div class="flip-card-container">
        <div class="flip-card" id="card">
            <div class="card-front">点击抽卡，翻开卡片</div>
            <div class="card-back">
                <div id="card-content"></div>
            </div>
        </div>
    </div>
    <br>
    <button id="draw">抽一张卡片</button>
    <button id="reset">重置新一轮</button>

<script>
const cardList = [
{"cn":"你觉得自己身上最值得骄傲的一件小事是什么？","en":"What is one small thing you are most proud of about yourself?","tip":"最近这学期、过去一年"},
{"cn":"如果可以学会一项新本领，你最想学会什么？","en":"If you could learn one new skill, what would it be?","tip":"运动、乐器、手工、语言"},
{"cn":"别人夸赞你的时候，你内心真实感受是什么？","en":"How do you truly feel when people praise you?","tip":"同学、老师、家人"},
{"cn":"有没有一件事，你明明努力了，但结果并不理想？","en":"Is there something you tried hard but did not get a good result?","tip":"考试、比赛、交朋友"},
{"cn":"你觉得自己在哪一方面还可以变得更好？","en":"In what area do you think you can improve?","tip":"时间管理、沟通、耐心"},
{"cn":"什么事情会让你真正感受到自信？","en":"What makes you feel truly confident?","tip":"完成任务、得到认可、独自解决难题"},
{"cn":"假如别人不理解你的爱好，你会在意吗？","en":"Would you care if others don’t understand your hobbies?","tip":"你的想法、你的选择"},
{"cn":"你什么时候会觉得自己状态最好？","en":"When do you feel you are at your best?","tip":"独处的时候、和朋友在一起的时候"},
{"cn":"你内心最害怕发生的一件小事是什么？","en":"What small thing are you most afraid of happening?","tip":"学校、生活当中"},
{"cn":"你更喜欢热闹的环境，还是安安静静的环境？为什么？","en":"Do you prefer busy or quiet surroundings? Why?","tip":"聚会、周末休息"},
{"cn":"如果不考虑现实条件，你长大想成为什么样的人？","en":"If there were no limits, what kind of person do you want to be?","tip":"职业、生活方式"},
{"cn":"你身上有哪些优点，是很少有人看得到的？","en":"What strengths do you have that few people notice?","tip":"性格、做事方式"},
{"cn":"做什么事情的时候，你会忘记时间？","en":"What activity makes you lose track of time?","tip":"看书、运动、创作"},
{"cn":"你如何看待自己的缺点？","en":"How do you think about your weaknesses?","tip":"急躁、害羞、容易放弃"},
{"cn":"如果可以给过去的自己说一句话，你会说什么？","en":"If you could say one sentence to your past self, what would you say?","tip":"一年前、刚上初中的时候"},
{"cn":"成功对你来说到底意味着什么？","en":"What does success really mean to you?","tip":"分数、朋友、过得开心"},
{"cn":"你觉得勇敢，代表从来不害怕吗？","en":"Do you think being brave means never feeling scared?","tip":"犯错、承认错误"},
{"cn":"哪些东西对你来说是最重要的？排2个出来。","en":"Name two things that matter most to you.","tip":"家人、自由、尊重、爱好"},
{"cn":"当你很累的时候，你会用什么方式照顾自己？","en":"What do you do to take care of yourself when you are tired?","tip":"心情不好、压力大"},
{"cn":"你希望别人怎样对待你？","en":"How do you wish other people would treat you?","tip":"同学、朋友、家人"},
{"cn":"你和好朋友最大的不同在哪里？","en":"What is the biggest difference between you and your best friend?","tip":"兴趣、性格、看待事情的想法"},
{"cn":"有没有别人开玩笑，但是让你觉得不舒服的经历？","en":"Have you had an experience where someone joked but made you uncomfortable?","tip":"被起外号、被调侃"},
{"cn":"朋友难过的时候，你会做什么去安慰他？","en":"What would you do to comfort a sad friend?","tip":"生气、委屈、受挫"},
{"cn":"朋友做什么事，会让你觉得被背叛？","en":"What would a friend do to make you feel betrayed?","tip":"撒谎、泄露秘密"},
{"cn":"你认为真正好朋友需要事事想法一样吗？","en":"Do real best friends need to agree on everything?","tip":"爱好、观点"},
{"cn":"如果好朋友做错了事，你敢当面指出来吗？为什么？","en":"Would you point it out if your good friend did something wrong? Why?","tip":"伤害别人、不守规则"},
{"cn":"你有没有为朋友保守过秘密，发生了什么？","en":"Have you ever kept a secret for a friend? What happened?","tip":"开心的秘密、难过的秘密"},
{"cn":"什么原因会让你主动结束一段友谊？","en":"What reason would make you end a friendship?","tip":"不尊重、总是消耗你"},
{"cn":"当大家都排挤某一个同学，你会怎么做？","en":"What would you do if everyone excluded one classmate?","tip":"玩笑起哄"},
{"cn":"新到一个集体，你会怎么认识新朋友？","en":"How do you make new friends in a new group?","tip":"班级、社团"},
{"cn":"朋友比你优秀的时候，你内心是什么感受？","en":"How do you feel when your friend does better than you?","tip":"成绩、运动、才艺"},
{"cn":"你被朋友误会，你会怎么处理？","en":"What will you do when your friend misunderstands you?","tip":"被冤枉"},
{"cn":"朋友总找你倾诉负面情绪，你会怎么办？","en":"What if a friend always dumps negative feelings on you?","tip":"抱怨、诉苦"},
{"cn":"你觉得人缘好=讨好所有人吗？说说你的想法。","en":"Is being popular equal to pleasing everyone? Share your thought.","tip":"班级社交"},
{"cn":"如果朋友邀请你做一件你不想做的事，你怎么拒绝？","en":"How do you say no when your friend asks you to do something you dislike?","tip":"出去玩、抄作业"},
{"cn":"什么样的行为算是善意，什么样算是老好人？","en":"What behaviour is kindness, and what is people‑pleasing?","tip":"同学相处"},
{"cn":"从同学变成好朋友，对方身上要有什么特质？","en":"What qualities turn a classmate into a close friend?","tip":"信任、真诚"},
{"cn":"你有没有和朋友发生矛盾，最后怎么解决的？","en":"Have you argued with a friend? How did you resolve it?","tip":"争吵、意见不合"},
{"cn":"你可以接受朋友拥有别的好朋友吗？","en":"Can you accept that your best friend has other close friends?","tip":"占有欲"},
{"cn":"如果看到同学被嘲笑，你的内心活动是什么？","en":"What goes through your mind when you see a classmate being teased?","tip":"外貌、成绩"},
{"cn":"说一说，你最欣赏妈妈哪一点？","en":"What do you admire most about your mum?","tip":"爸爸、爷爷奶奶、老师、偶像"},
{"cn":"如果爸爸妈妈很难过，你可以做些什么？","en":"What could you do if mum and dad feel sad?","tip":"生气、疲惫、吵架"},
{"cn":"家人做过哪件小事，让你内心觉得温暖？","en":"What small thing family did warmed your heart?","tip":"陪伴、支持"},
{"cn":"你希望家人多理解你的哪些想法？","en":"What thoughts of yours do you hope your family can understand better?","tip":"压力、爱好、交友"},
{"cn":"你和家人意见不一样的时候，你会怎么表达？","en":"How do you speak up when you disagree with your family?","tip":"学习安排、时间规划"},
{"cn":"家里谁最容易惹你生气，发生什么情况？","en":"Who in family upsets you most, under what situation?","tip":"兄弟姐妹"},
{"cn":"家人的哪些行为会让你感受到被尊重？","en":"What family behaviours make you feel respected?","tip":"倾听、不随意否定"},
{"cn":"你想对家里某个人说，但是一直没说出口的话是什么？","en":"What is something you want to tell a family member but never said?","tip":"感谢、委屈"},
{"cn":"你觉得家人之间可以有秘密吗？","en":"Is it okay for family members to keep some secrets?","tip":"边界感"},
{"cn":"回忆一件全家很快乐的往事。","en":"Recall one happy memory of our whole family.","tip":"旅行、过节、普通周末"},
{"cn":"你觉得父母身上也会有缺点吗？你怎么看待？","en":"Do you think parents also have weaknesses? What do you think?","tip":"犯错"},
{"cn":"如果你来安排家庭周末，你会怎么计划？","en":"If you plan our family weekend, what will you arrange?","tip":"假期"},
{"cn":"家人对你最大的期待是什么，那是你想要的吗？","en":"What is family’s biggest expectation for you, do you want that?","tip":"学习、未来"},
{"cn":"什么情况之下，你不愿意和家里人讲心里话？","en":"Under what circumstances you won’t share true feelings with family?","tip":"害怕被批评"},
{"cn":"你觉得兄弟姐妹带给你的收获是什么？","en":"What do you gain from having siblings?","tip":"没有兄弟姐妹就聊好朋友"},
{"cn":"家里发生冲突之后，你希望怎样修复关系？","en":"After family conflict, how do you hope we make things better?","tip":"吵架之后"},
{"cn":"哪一件家人的付出，以前你不懂，现在懂了？","en":"Which sacrifice from family you didn’t understand before but get it now?","tip":"日常照顾"},
{"cn":"你觉得亲子之间，边界感重要吗，体现在哪里？","en":"Is personal boundary important for parents‑kids? How does it show?","tip":"隐私、手机"},
{"cn":"你最想和家人一起尝试什么新事情？","en":"What new thing do you most want to try with our family?","tip":"运动、做饭、短途出游"},
{"cn":"如果可以改变家里的一个小规则，你想改成什么？","en":"If you can change one small family rule, what will it be?","tip":"作息、电子产品时间"},
{"cn":"什么事情最容易让你感到愤怒？","en":"What things make you feel angry most easily?","tip":"被误解、被打扰"},
{"cn":"生气的时候，你身体会出现什么样的感受？","en":"What physical feelings do you get when you are angry?","tip":"紧张、委屈"},
{"cn":"当你内心很委屈，你会选择说出来还是藏心里？为什么？","en":"When you feel wronged, will you speak out or keep it inside? Why?","tip":"害怕冲突"},
{"cn":"你觉得负面情绪是一件坏事吗？","en":"Do you think negative feelings are bad things?","tip":"难过、焦虑"},
{"cn":"压力很大的时候，有什么办法可以帮到你？","en":"What helps you when you are under lots of pressure?","tip":"考试压力、人际压力"},
{"cn":"你会嫉妒别人吗，嫉妒出现的时候你怎么看待自己？","en":"Do you feel jealous? How do you treat yourself when jealousy comes?","tip":"同学取得好成绩"},
{"cn":"明明心里很难受，但是假装没事，你有过吗？","en":"Have you pretended everything is fine when you actually feel bad?","tip":"在学校的时候"},
{"cn":"如果别人向你发脾气，你会怎么应对？","en":"How would you respond if someone loses temper towards you?","tip":"同学、家人"},
{"cn":"什么场景你容易焦虑，你怎样安抚自己？","en":"What situation makes you anxious, how do you calm yourself down?","tip":"当众发言、考试"},
{"cn":"原谅别人，是不是等于认同他做错的行为？","en":"Does forgiving someone mean you agree with their wrong action?","tip":"被伤害之后"},
{"cn":"一件事搞砸之后，你的内心独白是什么？","en":"What do you tell yourself when you mess something up?","tip":"失败"},
{"cn":"当你感到紧张，身体会有哪些信号？","en":"What body signals do you get when you feel nervous?","tip":"手心出汗、心跳快"},
{"cn":"你会因为害怕失败而不敢尝试吗？举个例子。","en":"Do you avoid trying things for fear of failure? Give an example.","tip":"新挑战"},
{"cn":"快乐一定要来自取得成功吗？","en":"Does happiness only come from being successful?","tip":"普通日常"},
{"cn":"你会把坏情绪发泄在身边亲近的人身上吗？怎么觉察？","en":"Do you take bad feelings out on people close to you? How do you notice?","tip":"疲惫的时候"},
{"cn":"别人安慰你的话，哪些对你真正管用？哪些没用？","en":"Which comforting words work for you, which do not help?","tip":"难过受挫"},
{"cn":"你允许自己有“不开心”的权利吗？","en":"Do you allow yourself the right to feel unhappy?","tip":"不必时刻积极"},
{"cn":"一件事已经过去了，但是你还是反复回想，怎么办？","en":"What to do if you keep replaying something that’s already over?","tip":"尴尬、受委屈"},
{"cn":"当你看到身边人情绪崩溃，你可以做什么？","en":"What can you do when someone around you breaks down emotionally?","tip":"陪伴朋友"},
{"cn":"如何区分“我只是心情不好”和“我需要找人求助”？","en":"How do you tell apart “just a bad mood” and “I need help”?","tip":"抑郁低落"},
{"cn":"勇敢是不是代表什么危险事情都敢做？","en":"Does being brave mean daring to do all dangerous things?","tip":"冲动、冒险"},
{"cn":"别人说你的看法不对，就代表你是错的吗？","en":"If others say your opinion is wrong, does that mean you are wrong?","tip":"观点分歧"},
{"cn":"为什么买东西之前要看一看价格？","en":"Why should we check price before buying things?","tip":"欲望、需要"},
{"cn":"你觉得“公平”是什么样子？生活里完全公平存在吗？","en":"What does fairness mean to you? Is total fairness real in life?","tip":"班级、家庭"},
{"cn":"你接触过哪些职业，你对哪个最好奇？","en":"What jobs have you seen? Which one makes you curious?","tip":"老师、医生、建筑工人"},
{"cn":"什么情况下，撒谎是绝对不可以的？","en":"Under which circumstances must we never tell lies?","tip":"有没有善意的谎言"},
{"cn":"犯错最大的价值是什么？","en":"What is the biggest value of making mistakes?","tip":"学到教训"},
{"cn":"付出努力，就一定得到想要的结果吗？你怎么看待？","en":"Hard work guarantees the result you want? What is your view?","tip":"现实"},
{"cn":"别人对你的评价，可以完全定义你这个人吗？","en":"Can other people’s comments fully define who you are?","tip":"夸奖、批评"},
{"cn":"规则存在的意义是什么？有没有规则不合理的时候？","en":"What is the point of rules? Are there times rules are unfair?","tip":"学校、社会"},
{"cn":"金钱可以买到哪些东西，哪些东西买不到？","en":"What can money buy? What cannot money buy?","tip":"幸福、尊重"},
{"cn":"竞争一定是坏事吗？竞争可以带来什么好处？","en":"Is competition always bad? What good can competition bring?","tip":"班级同学之间"},
{"cn":"什么叫责任？青少年要承担哪些责任？","en":"What is responsibility? What responsibilities do teenagers have?","tip":"对自己、对他人"},
{"cn":"如果时光可以倒流，改变一件过去的事，你改哪件？","en":"If you could turn back time and change one past event, what would it be?","tip":"不可以改变别人，只能改变自己"},
{"cn":"你觉得什么才算真正的善良？","en":"What do you think real kindness is?","tip":"不委屈自己的善良"},
{"cn":"方便大多数人的选择，就一定是正确的吗？","en":"Is a choice good for most people always the right one?","tip":"少数人的感受"},
{"cn":"学习的目的，仅仅是为拿到更高分数吗？","en":"Is the purpose of studying only to get higher marks?","tip":"成长"},
{"cn":"网络上面别人说的话，都可以相信吗，如何分辨？","en":"Can we believe everything people say online? How do you tell?","tip":"短视频、评论"},
{"cn":"自由是不是代表想做什么就做什么？边界在哪里？","en":"Does freedom mean doing whatever you want? Where is the boundary?","tip":"不能伤害别人"},
{"cn":"回顾这一年，你的思想发生了什么变化？","en":"Looking back on this year, how has your thinking changed?","tip":"看待朋友、看待自己"},
];

// 已抽取索引池
let usedIndexes = [];
const cardDom = document.getElementById("card");
const cardContentDom = document.getElementById("card-content");
const drawBtn = document.getElementById("draw");
const resetBtn = document.getElementById("reset");
const usedCountText = document.getElementById("usedCount");

drawBtn.onclick = function(){
    // 如果卡片正在翻转，阻止重复点击
    if(cardDom.classList.contains("flipped")) return;

    // 全部抽完
    if(usedIndexes.length >= cardList.length){
        cardContentDom.innerHTML = `<p>✅ 本轮100张卡片全部抽完！请点击重置开启新一轮。</p>`;
        return;
    }

    // 随机选一个没有抽过的卡片
    let randomIdx;
    do {
        randomIdx = Math.floor(Math.random() * cardList.length);
    } while(usedIndexes.includes(randomIdx));

    usedIndexes.push(randomIdx);
    const card = cardList[randomIdx];
    const cardNumber = randomIdx + 1;
    usedCountText.innerText = usedIndexes.length;

    // 填入卡片背面内容，然后翻转
    cardContentDom.innerHTML = `
        <div class="num-tag">🎴 卡片编号：${cardNumber}</div>
        <div class="cn">${card.cn}</div>
        <div class="en">${card.en}</div>
        <div class="tip">💡替换词：${card.tip}</div>
    `;
    cardDom.classList.add("flipped");

    // ===== 修改这里：30000毫秒 = 30秒，看完自动翻回去 =====
    setTimeout(()=>{
        cardDom.classList.remove("flipped");
    },30000);
}

resetBtn.onclick = function(){
    usedIndexes = [];
    usedCountText.innerText = "0";
    cardDom.classList.remove("flipped");
    cardContentDom.innerHTML = ``;
}
</script>
</body>
</html>