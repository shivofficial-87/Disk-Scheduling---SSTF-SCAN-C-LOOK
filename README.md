# Disk-Scheduling---SSTF-SCAN-C-LOOK

#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

/* ==========================================
   COMMON SORTING FUNCTION
   Used by SCAN and C-LOOK algorithms
   ========================================== */

void sort(int arr[], int n)
{
    int i, j, temp;

    for (i = 0; i < n - 1; i++)
    {
        for (j = 0; j < n - i - 1; j++)
        {
            if (arr[j] > arr[j + 1])
            {
                temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}


/* ==========================================
   1. SSTF (SHORTEST SEEK TIME FIRST)
   ========================================== */

void SSTF(int req[], int n, int head)
{
    int visited[100] = {0};
    int totalSeek = 0;
    int i, j;

    printf("\nSSTF SEEK SEQUENCE: %d", head);

    for (i = 0; i < n; i++)
    {
        int min = INT_MAX;
        int index = -1;

        for (j = 0; j < n; j++)
        {
            if (!visited[j])
            {
                int dist = abs(head - req[j]);

                if (dist < min)
                {
                    min = dist;
                    index = j;
                }
            }
        }

        visited[index] = 1;
        totalSeek += min;
        head = req[index];

        printf(" -> %d", head);
    }

    printf("\nTotal Seek Time = %d\n", totalSeek);
}


/* ==========================================
   2. SCAN (ELEVATOR DISK SCHEDULING)
   ========================================== */

void SCAN(int req[], int n, int head, int diskSize)
{
    int i, totalSeek = 0;
    int pos = 0;

    sort(req, n);

    printf("\nSCAN SEEK SEQUENCE: %d", head);

    /* Find first request greater than or equal to head */
    while (pos < n && req[pos] < head)
    {
        pos++;
    }

    /* Move towards higher cylinders */
    for (i = pos; i < n; i++)
    {
        totalSeek += abs(head - req[i]);
        head = req[i];

        printf(" -> %d", head);
    }

    /* Move to the last cylinder of the disk */
    totalSeek += abs(head - (diskSize - 1));
    head = diskSize - 1;

    printf(" -> %d", head);

    /* Reverse direction and service remaining requests */
    for (i = pos - 1; i >= 0; i--)
    {
        totalSeek += abs(head - req[i]);
        head = req[i];

        printf(" -> %d", head);
    }

    printf("\nTotal Seek Time = %d\n", totalSeek);
}


/* ==========================================
   3. C-LOOK (CIRCULAR LOOK)
   ========================================== */

void CLOOK(int req[], int n, int head)
{
    int i, totalSeek = 0;
    int pos = 0;

    sort(req, n);

    /* Find first request greater than or equal to head */
    while (pos < n && req[pos] < head)
    {
        pos++;
    }

    printf("\nC-LOOK SEEK SEQUENCE: %d", head);

    /* Service requests towards higher cylinders */
    for (i = pos; i < n; i++)
    {
        totalSeek += abs(head - req[i]);
        head = req[i];

        printf(" -> %d", head);
    }

    /* Jump to the lowest pending request */
    if (pos > 0)
    {
        totalSeek += abs(head - req[0]);
        head = req[0];

        printf(" -> %d", head);
    }

    /* Continue servicing remaining requests */
    for (i = 1; i < pos; i++)
    {
        totalSeek += abs(head - req[i]);
        head = req[i];

        printf(" -> %d", head);
    }

    printf("\nTotal Seek Time = %d\n", totalSeek);
}


/* ==========================================
   MAIN FUNCTION
   ========================================== */

int main()
{
    int req[100];
    int n, head, diskSize;
    int choice, i;

    printf("Enter number of requests: ");
    scanf("%d", &n);

    printf("Enter request queue:\n");

    for (i = 0; i < n; i++)
    {
        scanf("%d", &req[i]);
    }

    printf("Enter initial head position: ");
    scanf("%d", &head);

    printf("\n===== DISK SCHEDULING MENU =====\n");
    printf("1. SSTF\n");
    printf("2. SCAN\n");
    printf("3. C-LOOK\n");

    printf("Enter your choice: ");
    scanf("%d", &choice);

    switch (choice)
    {
        case 1:
            SSTF(req, n, head);
            break;

        case 2:
            printf("Enter disk size: ");
            scanf("%d", &diskSize);

            SCAN(req, n, head, diskSize);
            break;

        case 3:
            CLOOK(req, n, head);
            break;

        default:
            printf("Invalid choice!\n");
    }

    return 0;
}
