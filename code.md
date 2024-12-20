#include <windows.h>
#include <stdio.h>

int main() {
    HANDLE hFile, hMapping;
    LPVOID lpBase;
    DWORD dwFileSize, dwBytesRead;
    int charCount = 0;
    const char *filePath = "example.txt"; 

    
    hFile = CreateFile(filePath, GENERIC_READ, 0, NULL, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, NULL);
    if (hFile == INVALID_HANDLE_VALUE) {
        printf("CreateFile failed (%d)\n", GetLastError());
        return 1;
    }

    
    dwFileSize = GetFileSize(hFile, NULL);
    if (dwFileSize == INVALID_FILE_SIZE) {
        printf("GetFileSize failed (%d)\n", GetLastError());
        CloseHandle(hFile);
        return 1;
    }

    
    hMapping = CreateFileMapping(hFile, NULL, PAGE_READONLY, 0, 0, NULL);
    if (hMapping == NULL) {
        printf("CreateFileMapping failed (%d)\n", GetLastError());
        CloseHandle(hFile);
        return 1;
    }

    
    lpBase = MapViewOfFile(hMapping, FILE_MAP_READ, 0, 0, 0);
    if (lpBase == NULL) {
        printf("MapViewOfFile failed (%d)\n", GetLastError());
        CloseHandle(hMapping);
        CloseHandle(hFile);
        return 1;
    }

    
    for (DWORD i = 0; i < dwFileSize; i++) {
        if (((char*)lpBase)[i] != '\0') {
            charCount++;
        }
    }

   
    printf("Number of characters in the file: %d\n", charCount);

    
    UnmapViewOfFile(lpBase);
    CloseHandle(hMapping);
    CloseHandle(hFile);

    return 0;
}
