<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CODM 創作者計畫報表</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:-apple-system,BlinkMacSystemFont,"Segoe UI","PingFang TC","Noto Sans TC",sans-serif;background:#f5f5f3;color:#1a1a18;min-height:100vh}
  .wrap{max-width:1100px;margin:0 auto;padding:2rem 1.5rem}
  h1{font-size:20px;font-weight:500;margin-bottom:4px}
  .sub{font-size:13px;color:#888780;margin-bottom:20px}
  .stats{display:grid;grid-template-columns:repeat(auto-fit,minmax(100px,1fr));gap:10px;margin-bottom:24px}
  .stat{background:#fff;border-radius:8px;padding:12px 14px;border:0.5px solid #e0dfd8}
  .stat-label{font-size:11px;color:#888780;margin-bottom:4px}
  .stat-val{font-size:22px;font-weight:500}
  .tabs{display:flex;border-bottom:0.5px solid #e0dfd8;margin-bottom:16px}
  .tab{background:none;border:none;padding:8px 16px;cursor:pointer;font-size:14px;color:#888780;font-weight:400;border-bottom:2px solid transparent;margin-bottom:-1px;font-family:inherit}
  .tab.active{color:#1a1a18;font-weight:500;border-bottom:2px solid #1a1a18}
  .filters{display:flex;gap:8px;margin-bottom:12px;flex-wrap:wrap}
  .filters input,.filters select{font-size:13px;padding:7px 10px;border:0.5px solid #b4b2a9;border-radius:8px;background:#fff;color:#1a1a18;font-family:inherit;outline:none}
  .filters input{flex:1;min-width:160px}
  .filters input:focus,.filters select:focus{border-color:#888780}
  .count{font-size:12px;color:#888780;margin-bottom:8px}
  .table-wrap{border:0.5px solid #e0dfd8;border-radius:12px;overflow:auto}
  table{width:100%;border-collapse:collapse;font-size:13px;table-layout:fixed}
  thead tr{background:#f1efe8;border-bottom:0.5px solid #e0dfd8}
  th{padding:9px 12px;text-align:left;font-weight:500;font-size:12px;color:#888780;cursor:pointer;user-select:none;white-space:nowrap}
  th:last-child{cursor:default}
  th:hover{color:#1a1a18}
  tbody tr{border-bottom:0.5px solid #e0dfd8}
  tbody tr:last-child{border-bottom:none}
  tbody tr:nth-child(even){background:#fafaf8}
  tbody tr:nth-child(odd){background:#fff}
  td{padding:8px 12px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
  .badge{display:inline-block;font-size:11px;padding:2px 7px;border-radius:4px;font-weight:500;white-space:nowrap}
  .yt{background:#fff0f0;color:#a32d2d;border:0.5px solid #f09595}
  .tt{background:#f0f0ff;color:#3C3489;border:0.5px solid #AFA9EC}
  .ig{background:#fff8ee;color:#854F0B;border:0.5px solid #FAC775}
  .ok{background:#EAF3DE;color:#3B6D11;border:0.5px solid #C0DD97}
  .hold{background:#FAEEDA;color:#854F0B;border:0.5px solid #FAC775}
  .out{background:#FCEBEB;color:#A32D2D;border:0.5px solid #F7C1C1}
  .note-cell{color:#888780;font-size:12px}
  a{color:#185FA5;text-decoration:none;font-size:12px}
  a:hover{text-decoration:underline}
  .empty{padding:28px;text-align:center;color:#888780}
  .sort-icon{font-size:9px;margin-left:2px;opacity:0.4}
  .sort-icon.active{opacity:1}
  @media(max-width:600px){
    th:nth-child(4){display:none}
    td:nth-child(4){display:none}
  }
</style>
</head>
<body>
<div class="wrap">
  <h1>CODM 創作者計畫</h1>
  <p class="sub">創作者名單管理報表</p>
  <div class="stats" id="stats"></div>
  <div class="tabs">
    <button class="tab active" onclick="switchTab(0)">名單總覽</button>
    <button class="tab" onclick="switchTab(1)">退出名單</button>
  </div>
  <div class="filters">
    <input type="text" id="search" placeholder="搜尋名稱/備註..." oninput="render()">
    <select id="plat" onchange="render()">
      <option>全部</option><option>YouTube</option><option>TikTok</option><option>Instagram</option>
    </select>
    <select id="stat" onchange="render()">
      <option>全部</option><option>合格</option><option>暫留</option>
    </select>
  </div>
  <div class="count" id="count"></div>
  <div class="table-wrap">
    <table>
      <thead id="thead"></thead>
      <tbody id="tbody"></tbody>
    </table>
  </div>
</div>
<script>
const DATA=[
  {p:"YouTube",s:"合格",n:"小辣椒Chili",note:"",url:"https://www.youtube.com/@chili_kaap"},
  {p:"YouTube",s:"合格",n:"AYJ_俞見",note:"與Moonlight CODM同人",url:"https://www.youtube.com/@AYJ_gaming"},
  {p:"YouTube",s:"合格",n:"Artzlusky 空凍",note:"",url:"https://www.youtube.com/@Artzlusky"},
  {p:"YouTube",s:"合格",n:"狐狸",note:"",url:"https://www.youtube.com/@yue718"},
  {p:"YouTube",s:"合格",n:"Rick米漿",note:"",url:"https://www.youtube.com/@Rick-fb9sm"},
  {p:"YouTube",s:"合格",n:"WuHui無悔",note:"與wuhu1gaming同人",url:"https://www.youtube.com/@WUHU1"},
  {p:"YouTube",s:"合格",n:"Starfruit楊桃",note:"",url:"https://www.youtube.com/@StarfruitCODM"},
  {p:"YouTube",s:"合格",n:"NICK-C",note:"",url:"https://www.youtube.com/@Nick-cGaming"},
  {p:"YouTube",s:"合格",n:"田山先生Tayama",note:"",url:"https://www.youtube.com/@Tayama0425"},
  {p:"YouTube",s:"合格",n:"泰勒 Taylor",note:"",url:"https://www.youtube.com/@泰勒Taylor"},
  {p:"YouTube",s:"合格",n:"取名字好難",note:"與CODM北極熊同人",url:"https://www.youtube.com/@取名字好難-g5f"},
  {p:"YouTube",s:"合格",n:"Jimmy",note:"與Jimmy同人不同平台",url:"https://www.youtube.com/@Jimmy-Gaming-YT"},
  {p:"YouTube",s:"合格",n:"Jason Max",note:"",url:"https://www.youtube.com/@JasonMax-tl1xy"},
  {p:"YouTube",s:"合格",n:"Realufxx",note:"跟ufxxx同人雙頻道",url:"https://www.youtube.com/@Realufxx"},
  {p:"YouTube",s:"合格",n:"云昕",note:"疑似與1LayZa同人",url:"https://www.youtube.com/@1lay2a78"},
  {p:"YouTube",s:"合格",n:"TtcRay",note:"",url:"https://www.youtube.com/@TtcRay0629"},
  {p:"YouTube",s:"合格",n:"BENNY",note:"疑似與Xu同人",url:"https://www.youtube.com/@BENNY-y6v"},
  {p:"YouTube",s:"合格",n:"沐雨橙風",note:"",url:"https://www.youtube.com/@TWTimmy"},
  {p:"YouTube",s:"合格",n:"從不打狙",note:"",url:"https://www.youtube.com/@YT從不打狙"},
  {p:"YouTube",s:"合格",n:"夢羽",note:"",url:"https://www.youtube.com/@Xiaomong666"},
  {p:"YouTube",s:"合格",n:"惜晴OuO",note:"",url:"https://www.youtube.com/@x1qing7"},
  {p:"YouTube",s:"合格",n:"小天Xiao Tian.",note:"",url:"https://www.youtube.com/@XiaoTian0116"},
  {p:"YouTube",s:"合格",n:"Cg倉鼠",note:"",url:"https://www.youtube.com/@cangshu0202"},
  {p:"YouTube",s:"合格",n:"賴丞丞 Lai chen",note:"",url:"https://www.youtube.com/@賴丞丞YT"},
  {p:"YouTube",s:"合格",n:"Kingston games share",note:"",url:"https://www.youtube.com/@kingli1605"},
  {p:"YouTube",s:"暫留",n:"Yie祥不到",note:"資深主播退遊中有興趣合作",url:"https://www.youtube.com/@YIE1250"},
  {p:"YouTube",s:"退出",n:"零 ZeRo",note:"退出：都打DF了",url:"https://www.youtube.com/@ZeRo-hj2fk"},
  {p:"YouTube",s:"退出",n:"NOoNe",note:"退出：8月後無發片",url:"https://www.youtube.com/@NOoNe7F"},
  {p:"YouTube",s:"退出",n:"魔法飛魚",note:"退出：7月後無發片",url:"https://www.youtube.com/@魔法飛魚"},
  {p:"TikTok",s:"合格",n:"王俊卡",note:"",url:"https://www.tiktok.com/@codm.kaka_"},
  {p:"TikTok",s:"合格",n:"🔥Ch1Li🌶️",note:"",url:"https://www.tiktok.com/@ch1li_kaap"},
  {p:"TikTok",s:"合格",n:"千羽鶴QianYuhe",note:"",url:"https://www.tiktok.com/@qianyuhe.221"},
  {p:"TikTok",s:"合格",n:"愛玉",note:"",url:"https://www.tiktok.com/@aiyu0223"},
  {p:"TikTok",s:"合格",n:"CODM蝦仁",note:"與蝦仁⁰²同人",url:"https://www.tiktok.com/@codm79263"},
  {p:"TikTok",s:"合格",n:"eVen",note:"",url:"https://www.tiktok.com/@even_7x7"},
  {p:"TikTok",s:"合格",n:"CODM.脩",note:"",url:"https://www.tiktok.com/@codm.shu"},
  {p:"TikTok",s:"合格",n:"蝦仁⁰²",note:"與CODM蝦仁同人",url:"https://www.tiktok.com/@shrimp_0113"},
  {p:"TikTok",s:"合格",n:"十二天👾",note:"",url:"https://www.tiktok.com/@l2day_"},
  {p:"TikTok",s:"合格",n:"Mav1er",note:"",url:"https://www.tiktok.com/@mav1er_"},
  {p:"TikTok",s:"合格",n:"泰迪",note:"",url:"https://www.tiktok.com/@moon_teddy_pro"},
  {p:"TikTok",s:"合格",n:"CODM三公分",note:"",url:"https://www.tiktok.com/@funny.three.cm"},
  {p:"TikTok",s:"暫留",n:"天龍",note:"學測暫退但現在發DF",url:"https://www.tiktok.com/@tainlong9701"},
  {p:"TikTok",s:"暫留",n:"MianXian",note:"流量高且應是中文觀眾",url:"https://www.tiktok.com/@mianxiancodm"},
  {p:"TikTok",s:"退出",n:"diagovo",note:"退出：9月後無發片",url:"https://www.tiktok.com/@diagovo"},
  {p:"TikTok",s:"退出",n:"Jimmy",note:"退出：TT 10月後無發片",url:"https://www.tiktok.com/@jimmy_.tiktok"},
  {p:"TikTok",s:"退出",n:"白猴",note:"退出：都打瓦了",url:"https://www.tiktok.com/@wei_060821"},
  {p:"TikTok",s:"退出",n:"水鬼惑惑",note:"退出：8月後無發片，爭議型創作者",url:"https://www.tiktok.com/@codm_pyke"},
  {p:"Instagram",s:"合格",n:"🔥Ch1Li🌶️",note:"",url:"https://www.instagram.com/ch1li_kaap"},
  {p:"Instagram",s:"合格",n:"佐伊7",note:"",url:"https://www.instagram.com/manbaway_.526"},
  {p:"Instagram",s:"合格",n:"WUHUI",note:"與WuHui無悔同人",url:"https://www.instagram.com/wuhu1gaming"},
  {p:"YouTube",s:"合格",n:"魚仔",note:"",url:"https://www.youtube.com/@huhliu"},
  {p:"YouTube",s:"合格",n:"給堂堂",note:"雙頻道跨平台",url:"https://www.youtube.com/@geitangtang"},
  {p:"YouTube",s:"合格",n:"小小Ra",note:"雙頻道跨平台",url:"https://www.youtube.com/@ryan_10.4"},
  {p:"YouTube",s:"合格",n:"麻薩末bruh",note:"與大麻_薩末bruh同人",url:"https://www.youtube.com/@Masamobruhhhh"},
  {p:"YouTube",s:"合格",n:"Vki",note:"",url:"https://www.youtube.com/@Vki-v07"},
  {p:"YouTube",s:"合格",n:"菜鳥",note:"",url:"https://www.youtube.com/@菜鳥Noobird"},
  {p:"YouTube",s:"合格",n:"喬治遊戲日常",note:"",url:"https://www.youtube.com/@遊戲日常ps喬治"},
  {p:"YouTube",s:"合格",n:"Xu",note:"疑似與BENNY同人",url:"https://www.youtube.com/@NNYBE-f3c"},
  {p:"YouTube",s:"合格",n:"阿翔",note:"",url:"https://www.youtube.com/@阿翔codm"},
  {p:"YouTube",s:"合格",n:"Codm 新手玩家",note:"",url:"https://www.youtube.com/@Codm新手玩家"},
  {p:"YouTube",s:"合格",n:"CODM-WoRLD.",note:"",url:"https://www.youtube.com/@CODM-WoRLD-711"},
  {p:"YouTube",s:"合格",n:"娛樂遊戲決勝",note:"",url:"https://www.youtube.com/@娛樂遊戲決勝"},
  {p:"YouTube",s:"合格",n:"修狗",note:"",url:"https://www.youtube.com/@短槍手狗勾"},
  {p:"YouTube",s:"合格",n:"Kaito",note:"改頻道名稱未告知",url:"https://www.youtube.com/@Kaito-codm-yume"},
  {p:"YouTube",s:"合格",n:"無身分",note:"",url:"https://www.youtube.com/@無身分"},
  {p:"YouTube",s:"合格",n:"小蘋狗Pinggo",note:"",url:"https://www.youtube.com/@lindachang2809"},
  {p:"YouTube",s:"合格",n:"Louis Codm",note:"",url:"https://www.youtube.com/@LouisCodmYT"},
  {p:"YouTube",s:"合格",n:"菜鳥龍龍",note:"水片軍團需要警告",url:"https://www.youtube.com/@龍龍-YT"},
  {p:"YouTube",s:"合格",n:"少年",note:"",url:"https://www.youtube.com/@woodiehu"},
  {p:"YouTube",s:"合格",n:"嵐其",note:"改頻道未告知",url:"https://www.youtube.com/@LanChiDaShuaiGe"},
  {p:"YouTube",s:"合格",n:"貓咪_codm",note:"",url:"https://www.youtube.com/@雨-r7m"},
  {p:"YouTube",s:"合格",n:"spc.墨火",note:"",url:"https://www.youtube.com/@墨火Spc"},
  {p:"YouTube",s:"合格",n:"Seyna",note:"",url:"https://www.youtube.com/@SeynaCODM"},
  {p:"YouTube",s:"合格",n:"CODM北極熊",note:"與取名字好難同人",url:"https://www.youtube.com/@100ms-sm3ky"},
  {p:"YouTube",s:"合格",n:"bai hu 白狐",note:"改頻道名稱未告知，質量好",url:"https://www.youtube.com/@白狐-baihu"},
  {p:"YouTube",s:"合格",n:"DM倒頭就睡",note:"頻道404，問題多需複查",url:"https://www.youtube.com/@maku_mythra"},
  {p:"YouTube",s:"合格",n:"Moonlight CODM",note:"與AYJ_俞見同人",url:"https://www.youtube.com/@MoonlightCODM-p3z"},
  {p:"YouTube",s:"合格",n:"黑色傳奇",note:"",url:"https://www.youtube.com/@69awa"},
  {p:"YouTube",s:"合格",n:"甲",note:"",url:"https://www.youtube.com/@only9ay"},
  {p:"YouTube",s:"合格",n:"天煞不嘻嘻",note:"",url:"https://www.youtube.com/@Tiansha777"},
  {p:"YouTube",s:"合格",n:"QQ嘻嘻",note:"",url:"https://www.youtube.com/@QQ嘻嘻"},
  {p:"YouTube",s:"合格",n:"realufxxx",note:"跟ufxx同人雙頻道",url:"https://www.youtube.com/@realufxxx"},
  {p:"YouTube",s:"合格",n:"LouieRick柏榆兄弟",note:"",url:"https://www.youtube.com/@louierickchannelyt9899"},
  {p:"YouTube",s:"合格",n:"shadow尋影",note:"頻道連結404",url:"https://www.youtube.com/@Shadowxunying"},
  {p:"YouTube",s:"合格",n:"豪戲上場 Gaming",note:"",url:"https://www.youtube.com/@Solok0507"},
  {p:"YouTube",s:"合格",n:"普通人",note:"",url:"https://www.youtube.com/@普通人099"},
  {p:"YouTube",s:"合格",n:"我是小丑",note:"",url:"https://www.youtube.com/@小丑ノ_ノ-b1k"},
  {p:"YouTube",s:"合格",n:"糯米",note:"",url:"https://www.youtube.com/@Yi_11_h"},
  {p:"YouTube",s:"合格",n:"Kiwi yt",note:"",url:"https://www.youtube.com/@Yangtao_Actinidia"},
  {p:"YouTube",s:"合格",n:"玥塵",note:"",url:"https://www.youtube.com/@玥塵-c2x"},
  {p:"YouTube",s:"合格",n:"Mr.Stranger",note:"",url:"https://www.youtube.com/@Mr.Stranger-CODM"},
  {p:"YouTube",s:"合格",n:"野狼yelang",note:"",url:"https://www.youtube.com/@yelang1"},
  {p:"YouTube",s:"合格",n:"三指玩家",note:"",url:"https://www.youtube.com/@兩指玩家"},
  {p:"YouTube",s:"合格",n:"AIR DEVIL空中惡魔",note:"",url:"https://www.youtube.com/@onganrong3980"},
  {p:"YouTube",s:"合格",n:"呦西",note:"頻道連結404",url:"https://www.youtube.com/@攻擊我的你有陰謀"},
  {p:"YouTube",s:"合格",n:"雲海中的Nick",note:"",url:"https://www.youtube.com/@雲海哥新手別虐"},
  {p:"YouTube",s:"合格",n:"遊",note:"",url:"https://www.youtube.com/@遊玩者-g8i"},
  {p:"YouTube",s:"合格",n:"防曬鍋貼 funsaigotai",note:"",url:"https://www.youtube.com/@防曬鍋貼funsaigotai"},
  {p:"YouTube",s:"合格",n:"雪狐snow fox",note:"",url:"https://www.youtube.com/@雪狐-way13"},
  {p:"YouTube",s:"暫留",n:"宇Yuu",note:"退出後2月重新申請",url:"https://www.youtube.com/@阿宇111"},
  {p:"YouTube",s:"暫留",n:"口水怪",note:"流量好且2月有回歸",url:"https://www.youtube.com/@WE1_2008"},
  {p:"YouTube",s:"暫留",n:"DFK-哲",note:"退出後3月有上片",url:"https://www.youtube.com/@DFK-chen_codm"},
  {p:"YouTube",s:"暫留",n:"CODM蝦仁YT",note:"與蝦仁⁰²同人",url:"https://www.youtube.com/@蝦仁-v4v"},
  {p:"YouTube",s:"暫留",n:"千夜越",note:"品質好流量好但宣傳代儲",url:"https://www.youtube.com/@qianyeyue"},
  {p:"TikTok",s:"合格",n:"說故事の顧得猫宁",note:"",url:"https://www.tiktok.com/@wang9725"},
  {p:"TikTok",s:"合格",n:"Wednesday嵐",note:"不同平台",url:"https://www.tiktok.com/@wednesday_lan"},
  {p:"TikTok",s:"合格",n:"BRN.",note:"非公開帳號",url:"https://www.tiktok.com/@codm_brn._7"},
  {p:"TikTok",s:"合格",n:"多喝熱水 Codm",note:"",url:"https://www.tiktok.com/@zzzz94302"},
  {p:"TikTok",s:"合格",n:"佑蜥Yoxie",note:"改頻道名稱未告知",url:"https://www.tiktok.com/@yushinlin5"},
  {p:"TikTok",s:"合格",n:"Shun",note:"",url:"https://www.tiktok.com/@shun8783"},
  {p:"TikTok",s:"合格",n:"給堂堂",note:"雙頻道跨平台",url:"https://www.tiktok.com/@geitangtang0205"},
  {p:"TikTok",s:"合格",n:"小小Ra",note:"雙頻道跨平台",url:"https://www.tiktok.com/@ryan_10.4"},
  {p:"TikTok",s:"合格",n:"wanglu._",note:"",url:"https://www.tiktok.com/@wanglu._"},
  {p:"TikTok",s:"合格",n:"厐",note:"",url:"https://www.tiktok.com/@x1ang.codm"},
  {p:"TikTok",s:"合格",n:"RNa.",note:"",url:"https://www.tiktok.com/@raaaanai.612"},
  {p:"TikTok",s:"合格",n:"瑞歐",note:"",url:"https://www.tiktok.com/@codm_rio"},
  {p:"TikTok",s:"合格",n:"12_zav1er",note:"",url:"https://www.tiktok.com/@12_zav1er"},
  {p:"TikTok",s:"合格",n:"幻月Johnny",note:"",url:"https://www.tiktok.com/@johnnyld007zny"},
  {p:"TikTok",s:"合格",n:"Yuclee",note:"",url:"https://www.tiktok.com/@eason9948142"},
  {p:"TikTok",s:"合格",n:"秋楓",note:"不斷借帳號給別人",url:"https://www.tiktok.com/@1234567890eric2"},
  {p:"TikTok",s:"合格",n:"Sanzu",note:"已回歸",url:"https://www.tiktok.com/@sanzu_codm1"},
  {p:"TikTok",s:"合格",n:"捉蝦",note:"",url:"https://www.tiktok.com/@sim0hayha_"},
  {p:"TikTok",s:"合格",n:"poco",note:"",url:"https://www.tiktok.com/@seawy.ra"},
  {p:"TikTok",s:"合格",n:"歪歪",note:"與CODM歪歪同人",url:"https://www.tiktok.com/@yy.codm"},
  {p:"TikTok",s:"合格",n:"小葵",note:"",url:"https://www.tiktok.com/@a960225n"},
  {p:"TikTok",s:"合格",n:"開心",note:"",url:"https://www.tiktok.com/@cc_awe"},
  {p:"TikTok",s:"合格",n:"你太美",note:"",url:"https://www.tiktok.com/@user54478437960054"},
  {p:"TikTok",s:"合格",n:"wfsirius0326",note:"",url:"https://www.tiktok.com/@wfsirius0326"},
  {p:"TikTok",s:"合格",n:"花開運轉",note:"",url:"https://www.tiktok.com/@1liilllli"},
  {p:"TikTok",s:"合格",n:"77",note:"",url:"https://www.tiktok.com/@je77lau"},
  {p:"TikTok",s:"合格",n:"廖嘉泰Ted",note:"",url:"https://www.tiktok.com/@liaojiatai0"},
  {p:"TikTok",s:"合格",n:"天桃",note:"",url:"https://www.tiktok.com/@why68930"},
  {p:"TikTok",s:"合格",n:"余光",note:"",url:"https://www.tiktok.com/@yuguang._.codm"},
  {p:"TikTok",s:"合格",n:"WAVE.紅茶",note:"",url:"https://www.tiktok.com/@useratik9s3elz"},
  {p:"TikTok",s:"合格",n:"霍爾",note:"",url:"https://www.tiktok.com/@haller0412"},
  {p:"TikTok",s:"合格",n:"廖Jia泰Ted",note:"與廖嘉泰同人",url:"https://www.tiktok.com/@jia62133"},
  {p:"TikTok",s:"合格",n:"彥文",note:"榜一大大",url:"https://www.tiktok.com/@ywwwwwwwwwwwwww.1"},
  {p:"TikTok",s:"合格",n:"九逃",note:"",url:"https://www.tiktok.com/@.r32085"},
  {p:"TikTok",s:"合格",n:"小宸",note:"",url:"https://www.tiktok.com/@chen_.0226"},
  {p:"TikTok",s:"合格",n:"白鴿",note:"找不到此帳號",url:"https://www.tiktok.com/@bai_523"},
  {p:"TikTok",s:"合格",n:"冬瓜茶",note:"",url:"https://www.tiktok.com/@zzz.970101"},
  {p:"TikTok",s:"合格",n:"07大鸽子",note:"",url:"https://www.tiktok.com/@cnm_.1487"},
  {p:"TikTok",s:"合格",n:"罗尼",note:"3月重新報名",url:"https://www.tiktok.com/@ronny17z"},
  {p:"TikTok",s:"合格",n:"YuChüan雨川",note:"",url:"https://www.tiktok.com/@yuchuan0_0"},
  {p:"TikTok",s:"合格",n:"恋爱脑",note:"",url:"https://www.tiktok.com/@lian.i.nao_"},
  {p:"TikTok",s:"合格",n:"Hs1ung",note:"",url:"https://www.tiktok.com/@hs1ung0603"},
  {p:"TikTok",s:"暫留",n:"小貓崽崽",note:"流量好且是女玩家",url:"https://www.tiktok.com/@mibao_6"},
  {p:"TikTok",s:"暫留",n:"昆咬",note:"因會考暫退到6月",url:"https://www.tiktok.com/@kunyaocodm"},
  {p:"TikTok",s:"暫留",n:"軒Xuan",note:"換頻道沒說但有在更新",url:"https://www.tiktok.com/@xuan___xuan0727"},
  {p:"TikTok",s:"退出",n:"TYR_Kunny",note:"退出：都打Roblox了",url:"https://www.tiktok.com/@tyr_kunny"},
  {p:"TikTok",s:"退出",n:"Jx88",note:"退出：7月後無發片",url:"https://www.tiktok.com/@a113388"},
  {p:"TikTok",s:"退出",n:"17^",note:"退出：10月後無發片",url:"https://www.tiktok.com/@wxyu8_"},
  {p:"TikTok",s:"退出",n:"Naze",note:"退出：8月後無發片",url:"https://www.tiktok.com/@7znexi"},
  {p:"TikTok",s:"退出",n:"徒",note:"退出：9月後無發片",url:"https://www.tiktok.com/@user3p5269"},
  {p:"TikTok",s:"退出",n:"k13n",note:"退出：都打瓦了",url:"https://www.tiktok.com/@kelvincodm2025"},
  {p:"TikTok",s:"退出",n:"有理數",note:"退出：8月後無發片",url:"https://www.tiktok.com/@true_number"},
  {p:"TikTok",s:"退出",n:"威力是隻魚",note:"退出：10月後無發片",url:"https://www.tiktok.com/@willyisafish"},
  {p:"TikTok",s:"退出",n:"Brush",note:"退出：10月後無發片",url:"https://www.tiktok.com/@codm_brush"},
  {p:"YouTube",s:"合格",n:"陌謹_mojin",note:"與陌謹MoJin同人同平台",url:"https://www.youtube.com/@陌謹_mojin_YT"},
  {p:"YouTube",s:"合格",n:"我愛紅茶冰",note:"2月申請退遊",url:"https://www.youtube.com/@我愛紅茶冰"},
  {p:"YouTube",s:"合格",n:"Sunmon",note:"",url:"https://www.youtube.com/@Sunmon-q2r"},
  {p:"YouTube",s:"合格",n:"AbnerYT",note:"1月申請退遊",url:"https://www.youtube.com/@AbnerYT-yh"},
  {p:"YouTube",s:"合格",n:"很會泡槍的Ray",note:"",url:"https://www.youtube.com/@Raygogocodm"},
  {p:"YouTube",s:"合格",n:"熊貓CODM",note:"",url:"https://www.youtube.com/@熊貓1102"},
  {p:"YouTube",s:"合格",n:"Omelette",note:"",url:"https://www.youtube.com/@omelette0915"},
  {p:"YouTube",s:"合格",n:"Hanson",note:"",url:"https://www.youtube.com/@hanson_codm"},
  {p:"YouTube",s:"合格",n:"推星野 Max",note:"",url:"https://www.youtube.com/@Max-r7i7u"},
  {p:"YouTube",s:"合格",n:"Joe",note:"",url:"https://www.youtube.com/@Joe-987"},
  {p:"YouTube",s:"合格",n:"拉屎多",note:"",url:"https://www.youtube.com/@拉屎多"},
  {p:"YouTube",s:"合格",n:"Sean",note:"改頻道名稱未告知",url:"https://www.youtube.com/@Sean-666YT"},
  {p:"YouTube",s:"合格",n:"IAm_Fire_YT",note:"",url:"https://www.youtube.com/@Fire_X_Elite"},
  {p:"YouTube",s:"合格",n:"鍋貼(CODM)",note:"",url:"https://www.youtube.com/@KURT-CODM-鍋貼"},
  {p:"YouTube",s:"合格",n:"清鯊",note:"態度不佳改頻道名稱未告知",url:"https://www.youtube.com/@shark_726"},
  {p:"YouTube",s:"合格",n:"YOSHAAAA",note:"",url:"https://www.youtube.com/@CODM-YOSHAAAA"},
  {p:"YouTube",s:"合格",n:"心態codm",note:"",url:"https://www.youtube.com/@心態-t9d"},
  {p:"YouTube",s:"合格",n:"Mocoo Lin",note:"",url:"https://www.youtube.com/@mocoo_030"},
  {p:"YouTube",s:"合格",n:"Peng",note:"",url:"https://www.youtube.com/@pengcodm"},
  {p:"YouTube",s:"合格",n:"SOYO",note:"",url:"https://www.youtube.com/@soyorin-p4w"},
  {p:"YouTube",s:"合格",n:"无顏",note:"",url:"https://www.youtube.com/@決勝時刻布丁奶茶"},
  {p:"YouTube",s:"合格",n:"大麻_薩末bruh",note:"與麻薩末bruh同人",url:"https://www.youtube.com/@大麻_薩末_bruh"},
  {p:"YouTube",s:"合格",n:"枫糖",note:"",url:"https://www.youtube.com/@david.c3072"},
  {p:"YouTube",s:"合格",n:"狙の代言人",note:"",url:"https://www.youtube.com/@minggame945"},
  {p:"YouTube",s:"合格",n:"ICBM_汐華初流",note:"",url:"https://www.youtube.com/@JoegodJoestar"},
  {p:"YouTube",s:"合格",n:"Yuiz Archive",note:"",url:"https://www.youtube.com/@codm_yuiz"},
  {p:"YouTube",s:"合格",n:"味曾湯",note:"",url:"https://www.youtube.com/@味曾湯"},
  {p:"YouTube",s:"合格",n:"佑佑子君",note:"改頻道未告知頻道404",url:"https://www.youtube.com/@佑佑子君"},
  {p:"YouTube",s:"合格",n:"YZ game",note:"",url:"https://www.youtube.com/@YZ_game"},
  {p:"YouTube",s:"合格",n:"夢程 遊戲實況",note:"",url:"https://www.youtube.com/@夢程-2929"},
  {p:"YouTube",s:"合格",n:"HANK",note:"",url:"https://www.youtube.com/@HANK_CODM"},
  {p:"YouTube",s:"合格",n:"蓮霧很瘋",note:"",url:"https://www.youtube.com/@蓮霧很瘋"},
  {p:"YouTube",s:"合格",n:"一坨傑瑞ABOJ",note:"改頻道名稱未告知",url:"https://www.youtube.com/@heimin-Kaitakusha-jerrycodm"},
  {p:"YouTube",s:"合格",n:"冰奎",note:"",url:"https://www.youtube.com/@冰奎"},
  {p:"YouTube",s:"合格",n:"月空YT moonsky",note:"",url:"https://www.youtube.com/@月空owo"},
  {p:"YouTube",s:"合格",n:"青序Toweker",note:"",url:"https://www.youtube.com/@Toweker"},
  {p:"YouTube",s:"合格",n:"Konyiko",note:"",url:"https://www.youtube.com/@Konyiko-r3g"},
  {p:"YouTube",s:"合格",n:"YOYO GAMES",note:"",url:"https://www.youtube.com/@YOYOGAMESTW"},
  {p:"YouTube",s:"合格",n:"YT_KAI.蘇西",note:"",url:"https://www.youtube.com/@YT_KAI.蘇西"},
  {p:"YouTube",s:"合格",n:"五指玩家",note:"",url:"https://www.youtube.com/@Bigsea大海"},
  {p:"YouTube",s:"合格",n:"CODM.羽毛蒼蠅",note:"改頻道名稱未告知",url:"https://www.youtube.com/@IAMQZ0658"},
  {p:"YouTube",s:"合格",n:"STL_Yap",note:"",url:"https://www.youtube.com/@maxmax_shadowkiller"},
  {p:"YouTube",s:"合格",n:"零度",note:"",url:"https://www.youtube.com/@eri-e5n"},
  {p:"YouTube",s:"合格",n:"Ght_brw",note:"",url:"https://www.youtube.com/@Ght-brw"},
  {p:"YouTube",s:"合格",n:"愛玩的香貓",note:"",url:"https://www.youtube.com/@愛玩的小香貓"},
  {p:"YouTube",s:"合格",n:"AetherCheng艾埕",note:"",url:"https://www.youtube.com/@AetherCheng212"},
  {p:"YouTube",s:"合格",n:"sumo",note:"",url:"https://www.youtube.com/@Sumo.7788"},
  {p:"YouTube",s:"合格",n:"歪歐U麗欸哀",note:"",url:"https://www.youtube.com/@歪歐U麗欸哀"},
  {p:"YouTube",s:"合格",n:"SMK.中級菜鳥",note:"",url:"https://www.youtube.com/@1中級菜鳥菜鳥"},
  {p:"YouTube",s:"合格",n:"赤月コヨリ",note:"改頻道名稱未告知",url:"https://www.youtube.com/@akatsuki-koyori-codm"},
  {p:"YouTube",s:"合格",n:"wednesday嵐",note:"",url:"https://www.youtube.com/@wednesday_lan"},
  {p:"YouTube",s:"合格",n:"非洲部落長公主",note:"",url:"https://www.youtube.com/@非洲部落長公主"},
  {p:"YouTube",s:"合格",n:"陌謹MoJin",note:"與陌謹_mojin同人",url:"https://www.youtube.com/@陌謹的第二頻道"},
  {p:"YouTube",s:"合格",n:"game good",note:"",url:"https://www.youtube.com/@gamegood-n7c"},
  {p:"YouTube",s:"合格",n:"YT宮藍電競頻道",note:"",url:"https://www.youtube.com/@YT宮藍電競頻道"},
  {p:"YouTube",s:"合格",n:"rty",note:"改頻道名稱未告知頻道404",url:"https://www.youtube.com/@rty-h1x"},
  {p:"YouTube",s:"合格",n:"童話",note:"",url:"https://www.youtube.com/@sownlwjwo28"},
  {p:"YouTube",s:"合格",n:"d1zzy.滴悸",note:"",url:"https://www.youtube.com/@d1zzy_codm"},
  {p:"YouTube",s:"合格",n:"不吃香菜",note:"",url:"https://www.youtube.com/@不吃香菜哈"},
  {p:"YouTube",s:"合格",n:"白狐_Game",note:"",url:"https://www.youtube.com/@Whitef1208"},
  {p:"YouTube",s:"合格",n:"Kisbal",note:"與WAVE.紅茶同人不同平台",url:"https://www.youtube.com/@Kisbla-Hpewk-vk2pj"},
  {p:"YouTube",s:"合格",n:"狙remurins",note:"",url:"https://www.youtube.com/@Gremurins"},
  {p:"YouTube",s:"合格",n:"朗·加路",note:"",url:"https://www.youtube.com/@朗·加路codm"},
  {p:"YouTube",s:"合格",n:"Arhao 阿浩實況",note:"",url:"https://www.youtube.com/@Arhao02"},
  {p:"YouTube",s:"合格",n:"歪踢",note:"",url:"https://www.youtube.com/@歪踢der頻道"},
  {p:"YouTube",s:"退出",n:"夜影Night Shadow",note:"退出：9月後無發片",url:"https://www.youtube.com/@nightshadow夜影"},
  {p:"YouTube",s:"退出",n:"CODM小全",note:"退出：9月後無發片",url:"https://www.youtube.com/@Codm-j7q"},
  {p:"YouTube",s:"退出",n:"Willis Liao",note:"退出：9月後無發片",url:"https://www.youtube.com/@willisliao9338"},
  {p:"YouTube",s:"退出",n:"決勝描邊大師",note:"退出：10月後無發片",url:"https://www.youtube.com/@user-codmcute"},
  {p:"YouTube",s:"退出",n:"TA",note:"退出：9月後無發片",url:"https://www.youtube.com/@taan-0925"},
  {p:"YouTube",s:"退出",n:"怪物鸚鵡",note:"退出：9月後無發片",url:"https://www.youtube.com/@怪物鸚鵡"},
  {p:"YouTube",s:"退出",n:"Gg",note:"退出：9月後無發片",url:"https://www.youtube.com/@123w-j5j"},
  {p:"YouTube",s:"退出",n:"1LayZa",note:"退出：11月後無發片，疑似與云昕同人",url:"https://www.youtube.com/@1LayZa"},
  {p:"YouTube",s:"退出",n:"失望shiwang",note:"退出：9月後無發片",url:"https://www.youtube.com/@shiwang8303"},
  {p:"YouTube",s:"退出",n:"Zheng",note:"退出：9月後無發片",url:"https://www.youtube.com/@決勝時刻徒"},
  {p:"YouTube",s:"退出",n:"Kelvin",note:"退出：7月後無發片",url:"https://www.youtube.com/@Kelvincodm2025"},
  {p:"YouTube",s:"退出",n:"Koawa Kanade",note:"退出：7月後無發片",url:"https://www.youtube.com/@KoawaKanade"},
  {p:"YouTube",s:"退出",n:"年糕&阿拜頻道",note:"退出：7月後無發片",url:"https://www.youtube.com/@KouKiRar-666"},
  {p:"YouTube",s:"退出",n:"成哥",note:"退出：7月後無發片",url:"https://www.youtube.com/@成哥-t8f"},
  {p:"YouTube",s:"退出",n:"郭郭決勝",note:"退出：9月後無發片",url:"https://www.youtube.com/@kuoCODM"},
  {p:"YouTube",s:"退出",n:"poco",note:"退出：8月後無發片",url:"https://www.youtube.com/@Huangmike-e2v"},
  {p:"YouTube",s:"退出",n:"曉新",note:"退出：11月後無發片",url:"https://www.youtube.com/@Xiaoxin0813"},
  {p:"YouTube",s:"退出",n:"CODM歪歪",note:"退出：10月後無發片",url:"https://www.youtube.com/@user-tf8xy6ch7q"},
  {p:"YouTube",s:"退出",n:"ハスキー",note:"退出：7月後無發片",url:"https://www.youtube.com/@osekfmf-hv21"},
  {p:"YouTube",s:"退出",n:"Pini",note:"退出：7月後無發片",url:"https://www.youtube.com/@Pini-001"},
  {p:"YouTube",s:"退出",n:"阿健的游戲廳",note:"退出：11月後無發片",url:"https://www.youtube.com/@阿健-w7r"},
  {p:"YouTube",s:"退出",n:"Alvin",note:"退出：9月後無發片",url:"https://www.youtube.com/@mtsss-16p"},
  {p:"YouTube",s:"退出",n:"Werewolf狼人",note:"退出：9月後無發片",url:"https://www.youtube.com/@werewolfcodm"},
  {p:"YouTube",s:"退出",n:"Zm.",note:"退出：10月後無發片",url:"https://www.youtube.com/@Zm_.codm1020"},
  {p:"YouTube",s:"退出",n:"螃蟹成分複雜",note:"退出：10月後無發片",url:"https://www.youtube.com/@bruhholy0080"},
  {p:"YouTube",s:"退出",n:"插酒",note:"退出：11月後無發片",url:"https://www.youtube.com/@P.147"},
  {p:"YouTube",s:"退出",n:"Y.u",note:"退出：11月後無發片",url:"https://www.youtube.com/@austinyu160"},
  {p:"YouTube",s:"退出",n:"Zuni",note:"退出：11月後無發片",url:"https://www.youtube.com/@Zuni-zv7qd"},
  {p:"YouTube",s:"退出",n:"Lucien Nightfall",note:"退出：11月後無發片",url:"https://www.youtube.com/@LucienNightfall"},
  {p:"YouTube",s:"退出",n:"捆綁Play",note:"退出：11月後無發片",url:"https://www.youtube.com/@捆綁Play"},
  {p:"YouTube",s:"退出",n:"凱凱",note:"退出：10月後無發片",url:"https://www.youtube.com/@ÑØT-m4j"},
  {p:"YouTube",s:"退出",n:"Cody Tong",note:"退出：11月後無發片",url:"https://www.youtube.com/@CodyTong717"},
  {p:"YouTube",s:"退出",n:"Edwin Game",note:"退出：11月後無發片",url:"https://www.youtube.com/@Edwingame-9999"},
  {p:"YouTube",s:"退出",n:"江湖嫩咖小蝦米",note:"退出：10月後無發片",url:"https://www.youtube.com/@小蝦米-o1e"},
  {p:"YouTube",s:"退出",n:"洧魚罐頭",note:"退出：9月後無發片",url:"https://www.youtube.com/@洧魚罐頭-w1w"},
  {p:"YouTube",s:"退出",n:"AE坤之狗",note:"退出：都打DF了",url:"https://www.youtube.com/@shawn-h4b"},
  {p:"YouTube",s:"退出",n:"星奈",note:"退出：10月後無發片",url:"https://www.youtube.com/@star-hoshino"},
];

let curTab=0,sf="n",sd="asc";

function platClass(p){return p==="YouTube"?"yt":p==="TikTok"?"tt":"ig";}
function statClass(s){return s==="合格"?"ok":s==="暫留"?"hold":"out";}

function initStats(){
  const t=DATA.length,ok=DATA.filter(c=>c.s==="合格").length,hold=DATA.filter(c=>c.s==="暫留").length,out=DATA.filter(c=>c.s==="退出").length;
  const yt=DATA.filter(c=>c.p==="YouTube"&&c.s==="合格").length,tt=DATA.filter(c=>c.p==="TikTok"&&c.s==="合格").length,ig=DATA.filter(c=>c.p==="Instagram"&&c.s==="合格").length;
  document.getElementById("stats").innerHTML=[
    {l:"總人數",v:t},{l:"合格",v:ok},{l:"暫留",v:hold},{l:"退出",v:out},{l:"YouTube 合格",v:yt},{l:"TikTok 合格",v:tt},{l:"Instagram 合格",v:ig}
  ].map(s=>`<div class="stat"><div class="stat-label">${s.l}</div><div class="stat-val">${s.v}</div></div>`).join("");
}

function switchTab(i){
  curTab=i;
  document.querySelectorAll(".tab").forEach((t,idx)=>t.classList.toggle("active",idx===i));
  document.getElementById("stat").style.display=i===0?"":"none";
  render();
}

function sort(f){sf===f?sd=sd==="asc"?"desc":"asc":(sf=f,sd="asc");render();}

function render(){
  const search=document.getElementById("search").value.toLowerCase();
  const plat=document.getElementById("plat").value;
  const stat=document.getElementById("stat").value;
  let rows=DATA.filter(c=>{
    if(curTab===1&&c.s!=="退出")return false;
    if(curTab===0&&c.s==="退出")return false;
    if(plat!=="全部"&&c.p!==plat)return false;
    if(curTab===0&&stat!=="全部"&&c.s!==stat)return false;
    if(search&&!c.n.toLowerCase().includes(search)&&!c.note.toLowerCase().includes(search))return false;
    return true;
  });
  rows.sort((a,b)=>{
    let av=a[sf]||"",bv=b[sf]||"";
    return sd==="asc"?av.localeCompare(bv,"zh"):bv.localeCompare(av,"zh");
  });
  document.getElementById("count").textContent=`共 ${rows.length} 筆`;
  const si=(f)=>`<span class="sort-icon${sf===f?" active":""}">${sf===f&&sd==="desc"?"▼":"▲"}</span>`;
  document.getElementById("thead").innerHTML=`<tr>
    <th style="width:13%" onclick="sort('p')">平台${si("p")}</th>
    <th style="width:${curTab===0?"23%":"28%"}" onclick="sort('n')">頻道名稱${si("n")}</th>
    ${curTab===0?`<th style="width:10%" onclick="sort('s')">狀態${si("s")}</th>`:""}
    <th style="width:${curTab===0?"38%":"46%"}" onclick="sort('note')">備註${si("note")}</th>
    <th style="width:10%">連結</th>
  </tr>`;
  document.getElementById("tbody").innerHTML=rows.length===0
    ?`<tr><td colspan="5" class="empty">無符合條件的資料</td></tr>`
    :rows.map(c=>`<tr>
      <td><span class="badge ${platClass(c.p)}">${c.p}</span></td>
      <td style="font-weight:500">${c.n}</td>
      ${curTab===0?`<td><span class="badge ${statClass(c.s)}">${c.s}</span></td>`:""}
      <td class="note-cell">${c.note||"—"}</td>
      <td>${c.url?`<a href="${c.url}" target="_blank" rel="noopener noreferrer">前往</a>`:"—"}</td>
    </tr>`).join("");
}

initStats();
render();
</script>
</body>
</html>
