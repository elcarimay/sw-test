```cpp
// 적은 개수(50)의 노드에 대한 모든 상호 최단거리를 dist배열로 관리
// 간선이 추가될 때마다 업데이트
// 가능한 모든 배송 순서의 수(8!)를 확인하여 최소값 출력

int NN, dist[50][50];
bool keepgo;
#define INF 1'000'000'000

void init(int N, int E, int sCity[], int eCity[], int mTime[]){
    keepgo = false;
    NN = N;
    for (int i = 0; i < NN; i++)
        for (int j = 0; j < NN; j++)
            dist[i][j] = i - j ? INF : 0;
    for (int i = 0; i < E; i++)
        dist[sCity[i]][eCity[i]] = mTime[i];
}

void add(int sCity, int eCity, int mTime){
    keepgo = false;
    dist[sCity][eCity] = mTime;
}

void floyd(){
    keepgo = true;
    for (int k = 0; k < NN; k++)
        for (int i = 0; i < NN; i++)
            for (int j = 0; j < NN; j++)
                if (dist[i][j] > dist[i][k] + dist[k][j])
                    dist[i][j] = dist[i][k] + dist[k][j];
}

int S, MM, minP, arr[8], ss[8], ee[8];
bool visit[8];

void brute(int m, int n){
    if (n == MM) {
        int sum = dist[S][ss[arr[0]]];
        for (int i = 0; i < MM - 1; i++)
            sum += dist[ee[arr[i]]][ss[arr[i + 1]]];
        minP = minP < sum ? minP : sum; return;
    }
    for (int i = 0; i < MM; i++) {       // 순열
        if (!visit[i]) {
            arr[n] = i;
            visit[i] = true;
            brute(i, n + 1);
            visit[i] = false;
        }
    }
}

int deliver(int mPos, int M, int mSender[], int mReceiver[]){
    if (!keepgo) floyd();  // 간선 추가가 있을 경우에만 업데이트 
    S = mPos;
    MM = M;
    minP = INF;
    int fixed_sum = 0;
    for (int i = 0; i < MM; i++) {
        ss[i] = mSender[i];
        ee[i] = mReceiver[i];
        fixed_sum += dist[ss[i]][ee[i]];
    }
    brute(0, 0);  // 최단 배송순서 탐색
    return fixed_sum + minP;
}
```
