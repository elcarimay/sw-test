```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <string.h>

#define INIT 0
#define NEW_NODE 1
#define NEW_TASK 2
#define LIST_TASKS 3
#define TASK_STATUS 4

extern void init();
extern void destroy();
extern void newNode(int mTimestamp, char mNodeName[], char mNodeType[], int mSpeed);
extern void newTask(int mTimestamp, int mTaskId, char mNodeType[], int mWorkload, char mAssignedNode[]);
extern int listTasks(int mTimestamp, char mNodeName[], int mTaskIds[]);
extern int taskStatus(int mTimestamp, int mTaskId);

/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////

static int mTaskIds[100000];
static int task_ids_answer[100000];
static char mAssignedNode[20];
static char assigned_node_answer[20];

static int run(int Result)
{
    int N;
    scanf("%d", &N);
    init();
    for (int i = 0; i < N; ++i) {
        if (i == 58) {
            i = i;
        }
        int cmd = 0;
        int mTimestamp, mSpeed, mTaskId, mWorkload;
        int length, length_answer, status, status_answer;
        char mNodeName[20], mNodeType[20];

        scanf("%d", &cmd);
        switch (cmd) {
        case NEW_NODE:
            scanf("%d%s%s%d", &mTimestamp, mNodeName, mNodeType, &mSpeed);
            if (mTimestamp == 14033) {
                mTimestamp = mTimestamp;
            }
            newNode(mTimestamp, mNodeName, mNodeType, mSpeed);
            break;

        case NEW_TASK:
            scanf("%d%d%s%d", &mTimestamp, &mTaskId, mNodeType, &mWorkload);
            newTask(mTimestamp, mTaskId, mNodeType, mWorkload, mAssignedNode);
            scanf("%s", assigned_node_answer);
            if (strcmp(mAssignedNode, assigned_node_answer) != 0) {
                Result = 0;
            }
            break;

        case LIST_TASKS:
            scanf("%d%s", &mTimestamp, mNodeName);
            length = listTasks(mTimestamp, mNodeName, mTaskIds);
            scanf("%d", &length_answer);
            for (int i = 0; i < length_answer; i++)
                scanf("%d", &task_ids_answer[i]);
            if (length != length_answer) {
                Result = 0;
            }
            else {
                for (int i = 0; i < length; i++) {
                    if (mTaskIds[i] != task_ids_answer[i]) {
                        Result = 0;
                    }
                }
            }
            break;

        case TASK_STATUS:
            scanf("%d%d", &mTimestamp, &mTaskId);
            status = taskStatus(mTimestamp, mTaskId);
            scanf("%d", &status_answer);
            if (status != status_answer) {
                Result = 0;
            }
            break;

        }
    }
    destroy();
    return Result;
}

#include <time.h>

int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);

    int T, Result;
    scanf("%d %d", &T, &Result);
    for (int tc = 1; tc <= T; tc++) {
        printf("#%d %d\n", tc, run(Result));
    }
    printf("Performance: %d ms\n", clock() - start);
    return 0;
}

```
