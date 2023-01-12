# TCP 連線溝通方式

TCP 會在兩個端點間建立一個連線來確保雙方的溝通順暢，就像是一條電話專線一樣，在這個連線之中，會在來源與目的各指定一個 port (連接埠)，作為確認這個連線的編號。
由於 TCP 是基於 IP 的上面一層，TCP 的連線都在同一個 IP address 下，可以理解為都與同一台設備進行連線。但兩個設備間的連線可能不只一個，就會需要編號來分開不同連線。用 port 來命名編號，就像是在同一個地區(同一個 IP address)有不同的港口。

TCP 通訊過程可以分為三個階段。且必須正確建立連接在一個很多步驟的交握處理(handshake process)然後才進入建立連接(connection establishment)，再進入資料傳輸(data transfer)階段。資料傳輸完成，最後連接終止(connection termination)建立的虛擬通道關閉並釋放所有分配的資源。

一個 TCP 連接是由 OS 管理，TCP 連接基本上經歷底下這些變化：

1. listen: 如果是服務程式的話，指的是等待連接請求從任何遠端的客戶端。
2. syn-sent: 等待遠端點對點發回一個 TCP segment 並帶有 SYN 和 ACK flag。通常做這件事的為 TCP 客戶端。
3. syn-received: 等待遠端通道的另一端發回一個確認後，發回確認連接到遠端節點。通做這件事的為 TCP 服務端。
4. established: port 準備好接收/發送數據到遠端節點。
5. fin-wait-1
6. fin-wait-2
7. close-watt
8. closing
9. last-ack
10. time-wait: 指等待足後的時間，以確保通過遠端對等機器收到確認其連接終止請求。根據 RFC 793 中的連接，最大等待時間為四分鐘。
11. closed
