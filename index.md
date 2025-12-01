---
layout: default
title: Home
---

# 🛡️ Nuclei 공격 탐지 Snort Rules 아카이브

<table>
  <thead>
    <tr>
      <th style="width: 15%">CVE ID</th>
      <th style="width: 35%">Title</th>
      <th style="width: 15%">Nuclei</th>
      <th style="width: 15%">Pcap</th>
      <th style="width: 20%">Snort Rule</th>
    </tr>
  </thead>
  <tbody>
    {% for cve in site.cves %}
    <tr>
      <td style="font-weight:bold; color:#d9534f;">{{ cve.cve_id }}</td>

      <td>
        <a href="{{ cve.url | relative_url }}#info" style="text-decoration: none; color: #0366d6;">
          {{ cve.title }}
        </a>
      </td>

      <td style="text-align: center;">
        {% if cve.nuclei_url %}
        <a href="{{ cve.nuclei_url }}" target="_blank">🔗 Template</a>
        {% else %}
        -
        {% endif %}
      </td>

      <td style="text-align: center;">
        <a href="{{ '/pcaps/' | append: cve.slug | append: '.pcap' | relative_url }}" download style="text-decoration: none;" title="Download PCAP">
          🦈 Download
        </a>
      </td>

      <td style="text-align: center;">
        <a href="{{ cve.url | relative_url }}#rules" style="background-color: #f6f8fa; padding: 4px 8px; border: 1px solid #d1d5da; border-radius: 3px; text-decoration: none; font-size: 0.85em; color: #24292e;">
          📜 View Rules
        </a>
      </td>
    </tr>
    {% endfor %}
  </tbody>
</table>
3. 상세 페이지 레이아웃 수정 (_layouts/post.html)
여기가 핵심입니다. 탭 기능을 위한 CSS와 자바스크립트를 포함하고, 데이터를 예쁘게 뿌려주는 화면입니다.

Info 탭: Description, 메타데이터(CVSS, EPSS 등), References 표시

Rules 탭: 사용자가 작성한 Snort Rule 코드 블록 표시

주의: 아래 코드는 사용자가 .md 파일 본문에 Snort Rule 코드를 적었다고 가정하고, 화면상에서 탭으로 내용을 분리해서 보여줍니다.

HTML

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ page.cve_id }} - {{ page.title }}</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/github-markdown-css/5.1.0/github-markdown.min.css">
    <style>
        body { box-sizing: border-box; min-width: 200px; max-width: 980px; margin: 0 auto; padding: 45px; }
        @media (max-width: 767px) { body { padding: 15px; } }
        
        /* 탭 스타일 */
        .tab-container { margin-top: 20px; border-bottom: 1px solid #d0d7de; }
        .tab-button {
            background: none; border: none; padding: 10px 20px; font-size: 16px; cursor: pointer;
            color: #57606a; border-bottom: 2px solid transparent;
        }
        .tab-button.active { font-weight: bold; color: #0969da; border-bottom: 2px solid #fd8c73; }
        
        /* 내용 영역 스타일 */
        .content-section { display: none; padding-top: 20px; }
        .content-section.active { display: block; }

        /* 정보 테이블 스타일 */
        .meta-table { width: 100%; border-collapse: collapse; margin-bottom: 20px; }
        .meta-table th { text-align: left; background: #f6f8fa; padding: 10px; border: 1px solid #d0d7de; width: 30%; }
        .meta-table td { padding: 10px; border: 1px solid #d0d7de; }
        .tag { background: #ddf4ff; color: #0969da; padding: 2px 6px; border-radius: 4px; font-size: 0.85em; margin-right: 5px; }
    </style>
</head>
<body>
    <div class="markdown-body">
        <a href="{{ site.baseurl }}/" class="header-link" style="text-decoration:none;">🔙 목록으로 돌아가기</a>
        <h1>{{ page.cve_id }}</h1>
        <p style="font-size: 1.2em; color: #57606a;">{{ page.title }}</p>

        <div class="tab-container">
            <button class="tab-button" onclick="openTab('info')" id="btn-info">📋 Vulnerability Info</button>
            <button class="tab-button" onclick="openTab('rules')" id="btn-rules">🛡️ Snort Rules</button>
        </div>

        <div id="info" class="content-section">
            <table class="meta-table">
                <tr><th>Severity</th><td style="color: #cf222e; font-weight: bold;">{{ page.severity }}</td></tr>
                <tr><th>CVSS Score</th><td>{{ page.cvss_score }}</td></tr>
                <tr><th>EPSS Score</th><td>{{ page.epss_score }}</td></tr>
                <tr><th>Tags</th><td>
                    {% for tag in page.tags %} <span class="tag">{{ tag }}</span> {% endfor %}
                </td></tr>
            </table>

            <h3>Description</h3>
            <div id="desc-content">
                {{ content }}
            </div>

            {% if page.references %}
            <h3>References</h3>
            <ul>
                {% for ref in page.references %}
                <li><a href="{{ ref }}" target="_blank">{{ ref }}</a></li>
                {% endfor %}
            </ul>
            {% endif %}
            
            <br>
            <hr>
            {% if page.nuclei_url %}
            <p><strong>🔗 Nuclei Template:</strong> <a href="{{ page.nuclei_url }}" target="_blank">{{ page.nuclei_url }}</a></p>
            {% endif %}
             <p><strong>🦈 PCAP Download:</strong> <a href="{{ site.baseurl }}/pcaps/{{ page.slug }}.pcap" download>Download .pcap</a></p>
        </div>

        <div id="rules" class="content-section">
            <div class="warn" style="background-color: #fff8c5; padding: 10px; border-radius: 6px; margin-bottom: 15px; border: 1px solid #d4a72c;">
                ⚠️ <strong>Note:</strong> 이 룰은 탐지 목적으로 작성되었으며, 실제 환경 적용 시 튜닝이 필요할 수 있습니다.
            </div>
            
            <div id="rules-content-area">
                </div>
        </div>
    </div>

    <script>
        function openTab(tabName) {
            // 모든 탭 내용 숨기기
            var contents = document.getElementsByClassName("content-section");
            for (var i = 0; i < contents.length; i++) {
                contents[i].classList.remove("active");
            }
            // 모든 버튼 비활성화
            var buttons = document.getElementsByClassName("tab-button");
            for (var i = 0; i < buttons.length; i++) {
                buttons[i].classList.remove("active");
            }
            // 선택된 탭 활성화
            document.getElementById(tabName).classList.add("active");
            document.getElementById("btn-" + tabName).classList.add("active");
        }

        // 페이지 로드 시 URL 해시(#info 또는 #rules) 확인
        window.onload = function() {
            var hash = window.location.hash.substring(1); // # 제거
            if (hash === 'rules') {
                openTab('rules');
            } else {
                openTab('info');
            }

            // [자동 정리 스크립트]
            // 본문(info 탭)에 있는 Snort 코드(pre 태그)를 
            // 자동으로 Rules 탭으로 옮겨와서 깔끔하게 보여주는 기능
            var infoDiv = document.getElementById("desc-content");
            var rulesDiv = document.getElementById("rules-content-area");
            
            // h3 태그 중 'Snort'가 포함된 제목과 그 뒤에 오는 코드 블록들을 이동
            // (간단하게 모든 code block(pre)을 룰 탭으로 이동시키는 로직)
            var codeBlocks = infoDiv.querySelectorAll("pre, .highlighter-rouge");
            
            // 제목(h2, h3)도 같이 옮기고 싶다면 로직이 복잡해지므로, 
            // 여기서는 코드 블록만 깔끔하게 추출해서 이동시킵니다.
            codeBlocks.forEach(function(block) {
                // 해당 블록의 바로 앞 형제 요소가 제목(H2, H3)이면 같이 이동 (선택사항)
                var prev = block.previousElementSibling;
                if(prev && (prev.tagName === 'H3' || prev.tagName === 'H2') && prev.innerText.includes('Snort')) {
                    rulesDiv.appendChild(prev); 
                }
                rulesDiv.appendChild(block);
            });
        };
    </script>
</body>
</html>
