# HTTP/3 Server push

HTTP/3 的伺服器端推送與 HTTP/2 ([RFC7540](https://httpwg.org/specs/rfc7540.html)) 類似，但機制上有所不同。

伺服器端推送實際上是對客戶端從未發送過的請求的回應！

伺服器端推送僅在客戶端同意的前提下才允許發出。在 HTTP/3 中，客戶端甚至能通過告知伺服器端的最大推送流ID來設置所接受推送的次數限制。超出限制將導致連接錯誤。

如果伺服器端認為客戶端可能需要某個並未要求但應該有的額外資源，伺服器端可以通過請求流發送一個 `PUSH_PROMISE` frame，使該推送請求看上去像是一個回應，然後通過新的串流發送實際回應。

即便客戶端之前已經表示過可接受推送，但如果客戶端仍然可以隨時取消每個推送串流，然後發送一個 `CANCEL_PUSH` frame 到伺服器端。

## Problematic

自從推送這一特性在 HTTP/2 中討論、開發、部署以來，它就備受爭議、討論和抨擊。為了讓它有用，人們付出了許多努力。

推送從來不是 "免費" 的，儘管它省了半個往返的延遲，它還是會消耗帶寬。伺服器也很難或不可能從高層面確定一個資源是否應該被推送過去。