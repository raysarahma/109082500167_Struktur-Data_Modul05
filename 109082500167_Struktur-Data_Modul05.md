# <h1 align="center"> Laporan Praktikum Modul 5 - SINGLY LINKED LIST (BAGIAN KEDUA) </h1>

<p align="center"> Raysa Rahma Irahim - 109082500167 </p>

## Dasar Teori
### A. Searching pada Linked List<br/>
Searching adalah operasi buat mencari node tertentu dengan mengunjungi node satu per satu dari first sampai ketemu atau sampai Nil [1]. Karena singly linked list cuma bisa dibaca maju, pencariannya sekuensial dan tidak bisa langsung loncat ke tengah seperti array [2]. Alamat hasil pencarian dipakai oleh operasi lain seperti insert after dan delete after [1].
### 1. Fungsi Pencarian pada ADT<br/>
Ada tiga fungsi pencarian di modul. findElm(L, X) mencari elemen dengan info X dan mengembalikan alamatnya (Nil kalau tidak ada). fFindElm(L, P) mengecek apakah alamat P ada di list (true/false). findBefore(L, P) mengembalikan elemen sebelum P, atau Nil kalau P di awal list [1].

### B. Operasi Lanjutan pada Singly Linked List<br/>
### 1. Insert dan Delete<br/>
insertFirst, insertAfter, dan insertLast menambah elemen di awal, setelah elemen tertentu, dan di akhir list. Untuk hapus ada delFirst, delLast, delAfter, dan delP. delAfter menghapus elemen setelah Prec [1][2], sedangkan delP menghapus berdasarkan nilai info X dengan bantuan findElm [1].
### 2. Proses terhadap Seluruh List<br/>
delAll menghapus semua elemen, dan invertList membalik urutan list. copyList hanya membuat L2 memakai elemen yang sama dengan L1, sedangkan fCopyList membuat list baru berisi salinan elemennya [1].
### 3. Manajemen Memori <br/>
Elemen dibuat dengan new (alokasi) dan dibebaskan dengan delete (dealokasi). Elemen yang sudah dilepas dari list sebaiknya langsung didealokasi supaya tidak terjadi memory leak [3].

## Guided
### GUIDE 1

### list.h
```C++
#ifndef List_h
#define List_h
#define Nil NULL

#include <iostream>
using namespace std;

struct mahasiswa
{
    string nama, nim;
    int umur;
};

typedef mahasiswa dataMahasiswa;
typedef struct node *address;

struct node
{
    dataMahasiswa isidata;
    address next;
};

struct linkedList
{
    address first;
};

bool isEmpty(linkedList List);
void createList(linkedList &List);
address alokasi(string nama, string nim, int umur);
void dealokasi(address &node);
void printList(linkedList List);
void insertFirst(linkedList &List, address nodeBaru);
void insertLast(linkedList &List, address nodeBaru);
void insertAfter(linkedList &List, address nodeBaru, address prev);
void delFirst(linkedList &List);
void delLast(linkedList &List);
void delAfter(linkedList &List, address nodeHapus, address nodePrev);
int nbList(linkedList List);
void deleteList(linkedList &List);

address FindNodebyData(linkedList List, string data);
void FindNodebyAddress(linkedList List, address node);
void FindNodeByRange(linkedList List, int nilaiAwal, int nilaiAkhir);

void updateFirst(linkedList &list);
void updateLast(linkedList &list);
void updateAfter(linkedList &list, address &prev);
void updateByNim(linkedList &list, string nim);

#endif
```

### list.cpp
```C++
#include "list.h"
#include <iostream>
using namespace std;

// I.S = Initial State / kondisi awal
// F.S = Final State / kondisi akhir

// fungsi untuk cek apakah list kosong atau tidak
bool isEmpty(linkedList List)
{
    if (List.first == Nil)
    {
        return true;
    }
    else
    {
        return false;
    }
}

// pembuatan linked list kosong
void createList(linkedList &List)
{
    /* I.S. sembarang
       F.S. terbentuk list kosong */
    List.first = Nil;
}

// pembuatan node baru dengan menerapkan manajemen memori
address alokasi(string nama, string nim, int umur)
{
    /* I.S. sembarang
       F.S. mengembalikan alamat node baru dengan isidata = sesuai parameter dan next = Nil */
    address nodeBaru = new node;
    nodeBaru->isidata.nama = nama;
    nodeBaru->isidata.nim = nim;
    nodeBaru->isidata.umur = umur;
    nodeBaru->next = Nil;
    return nodeBaru;
}

// penghapusan node dengan menerapkan manajemen memori
void dealokasi(address &node)
{
    /* I.S. P terdefinisi
       F.S. memori yang digunakan node dikembalikan ke sistem */
    node->next = Nil;
    delete node;
}

// prosedur-prosedur untuk insert / menambahkan node baru kedalam list
void insertFirst(linkedList &List, address nodeBaru)
{
    /* I.S. sembarang, P sudah dialokasikan
       F.S. menempatkan elemen list (node) pada awal list */
    nodeBaru->next = List.first;
    List.first = nodeBaru;
}

void insertAfter(linkedList &List, address nodeBaru, address Prev)
{
    /* I.S. sembarang, nodeBaru dan Prev alamat salah satu elemen list (node)
       F.S. menempatkan elemen (node) sesudah elemen node Prev */
    if (Prev != Nil)
    {
        nodeBaru->next = Prev->next;
        Prev->next = nodeBaru;
    }
    else
    {
        cout << "Node sebelumnya tidak valid!" << endl;
    }
}

void insertLast(linkedList &List, address nodeBaru)
{
    /* I.S. sembarang, nodeBaru sudah dialokasikan
       F.S. menempatkan elemen nodeBaru pada akhir list */
    if (isEmpty(List))
    {
        List.first = nodeBaru;
    }
    else
    {
        address nodeBantu = List.first;
        while (nodeBantu->next != Nil)
        {
            nodeBantu = nodeBantu->next;
        }
        nodeBantu->next = nodeBaru;
    }
}

// prosedur-prosedur untuk delete / menghapus node yang ada didalam list
void delFirst(linkedList &List)
{
    /* I.S. list tidak kosong
    F.S. node pertama di list terhapus*/
    address nodeHapus;
    if (isEmpty(List) == false)
    {
        nodeHapus = List.first;
        List.first = List.first->next;
        nodeHapus->next = Nil;
        dealokasi(nodeHapus);
    }
    else
    {
        cout << "List kosong!" << endl;
    }
}

void delLast(linkedList &List)
{
    /* I.S. list tidak kosong
    F.S. node terakhir di list terhapus */
    address nodeHapus, nodePrev;
    if (isEmpty(List) == false)
    {
        nodeHapus = List.first;
        if (nodeHapus->next == Nil)
        {
            List.first->next = Nil;
            dealokasi(nodeHapus);
        }
        else
        {
            while (nodeHapus->next != Nil)
            {
                nodePrev = nodeHapus;
                nodeHapus = nodeHapus->next;
            }
            nodePrev->next = Nil;
            dealokasi(nodeHapus);
        }
    }
    else
    {
        cout << "list kosong" << endl;
    }
}

void delAfter(linkedList &List, address nodeHapus, address nodePrev)
{
    /* I.S. list tidak kosng, Prev alamat salah satu elemen list
    F.S. nodeBantu adalah alamat dari Prev→next, menghapus Prev→next dari list */
    if (isEmpty(List) == true)
    {
        cout << "List kosong!" << endl;
    }
    else
    { // jika list tidak kosong
        if (nodePrev != Nil && nodePrev->next != Nil)
        {
            nodeHapus = nodePrev->next;
            nodePrev->next = nodeHapus->next;
            nodeHapus->next = Nil;
            dealokasi(nodeHapus);
        }
        else
        {
            cout << "Node sebelumnya (prev) tidak valid!" << endl;
        }
    }
}

// prosedur untuk menampilkan isi list
void printList(linkedList List)
{
    /* I.S. list mungkin kosong
       F.S. jika list tidak kosong menampilkan semua info yang ada pada list */
    if (isEmpty(List))
    {
        cout << "List kosong." << endl;
    }
    else
    {
        address nodeBantu = List.first;
        while (nodeBantu != Nil)
        {
            cout << "Nama : " << nodeBantu->isidata.nama << ", NIM : " << nodeBantu->isidata.nim << ", Usia : " << nodeBantu->isidata.umur << endl;
            nodeBantu = nodeBantu->next;
        }
    }
}

// function untuk menampilkan jumlah node didalam list
int nbList(linkedList List)
{
    /* I.S. list sudah ada
       F.S. menampilkan jumlah node didalam list*/
    int count = 0;
    address nodeBantu = List.first;
    while (nodeBantu != Nil)
    {
        count++;
        nodeBantu = nodeBantu->next;
    }
    return count;
}

// prosedur untuk menghapus list (menghapus semua node didalam list)
void deleteList(linkedList &List)
{
    /* I.S. list sudah ada
       F.S. menghapus semua node didalam list*/
    address nodeBantu, nodeHapus;
    nodeBantu = List.first;
    while (nodeBantu != Nil)
    {
        nodeHapus = nodeBantu;
        nodeBantu = nodeBantu->next;
        dealokasi(nodeHapus);
    }
    List.first = Nil;
    cout << "List sudah terhapus!" << endl;
}

// prosedur-prosedur untuk melakukan update data node
void updateFirst(linkedList &list)
{
    dataMahasiswa newData;
    cout << "Masukan nama baru : ";
    cin >> newData.nama;
    cout << "Masukan nim baru : ";
    cin >> newData.nim;
    cout << "Masukan umur baru : ";
    cin >> newData.umur;
    list.first->isidata = newData;
}

void updateLast(linkedList &list)
{
    if (isEmpty(list))
    {
        cout << "List kosong!" << endl;
    }
    else
    {
        dataMahasiswa newData;
        cout << "Masukan nama baru : ";
        cin >> newData.nama;
        cout << "Masukan nim baru : ";
        cin >> newData.nim;
        cout << "Masukan umur baru : ";
        cin >> newData.umur;

        address nodeBantu = list.first;
        while (nodeBantu->next != Nil)
        {
            nodeBantu = nodeBantu->next;
        }

        nodeBantu->isidata = newData;
    }
}

void updateAfter(linkedList &list, address &prev)
{
    if (isEmpty(list))
    {
        cout << "List kosong!" << endl;
    }
    else
    {
        dataMahasiswa newData;
        cout << "Masukan nama baru : ";
        cin >> newData.nama;
        cout << "Masukan nim baru : ";
        cin >> newData.nim;
        cout << "Masukan umur baru : ";
        cin >> newData.umur;

        prev->next->isidata = newData;
    }
}

void updateByNim(linkedList &list, string nim)
{
    if (!isEmpty(list))
    {
        address nodeBantu = FindNodebyData(list, nim);

        if (nodeBantu != Nil)
        {
            dataMahasiswa newData;
            cout << "Masukan nama baru : ";
            cin >> newData.nama;
            cout << "Masukan nim baru : ";
            cin >> newData.nim;
            cout << "Masukan umur baru : ";
            cin >> newData.umur;

            nodeBantu->isidata = newData;
        }
    }
    else
    {
        cout << "List kosong!" << endl;
    }
}

// prosedur-prosedur untuk searching data didalam list
address FindNodebyData(linkedList List, string data)
{
    /* I.S. list mungkin kosong
       F.S. menampilkan node yang sesuai dengan nama atau nim yang dicari */
    if (isEmpty(List) == true)
    {
        cout << "List kosong!" << endl;
    }
    else
    {
        address nodeBantu = List.first;
        int posisi = 0;
        bool found = false;
        while (nodeBantu != Nil)
        {
            posisi++;
            if (nodeBantu->isidata.nama == data || nodeBantu->isidata.nim == data)
            {
                cout << "Data " << data << " ditemukan pada posisi ke-" << posisi << "!" << endl;
                cout << "Nama : " << nodeBantu->isidata.nama << ", NIM : " << nodeBantu->isidata.nim << ", Usia : " << nodeBantu->isidata.umur << endl;
                found = true;
                break;
            }
            nodeBantu = nodeBantu->next;
        }
        if (found == false)
        {
            cout << "Node dengan data " << data << " tidak ditemukan!" << endl;
        }
    }
    cout << endl;
    return Nil;
}

void FindNodebyAddress(linkedList List, address node)
{
    /* I.S. list mungkin kosong
       F.S. menampilkan node yang sesuai dengan alamat yang dicari */
    if (isEmpty(List) == true)
    {
        cout << "List kosong!" << endl;
    }
    else
    {
        address nodeBantu = List.first;
        int posisi = 0;
        bool found = false;
        while (nodeBantu != Nil)
        {
            posisi++;
            if (nodeBantu == node)
            {
                cout << "Node ditemukan pada posisi ke-" << posisi << "!" << endl;
                cout << "Alamat Node : " << nodeBantu << endl;
                cout << "Nama : " << nodeBantu->isidata.nama << ", NIM : " << nodeBantu->isidata.nim << ", Usia : " << nodeBantu->isidata.umur << endl;
                found = true;
                break;
            }
            nodeBantu = nodeBantu->next;
        }
        if (found == false)
        {
            cout << "Node dengan alamat " << node << " tidak ditemukan dalam list!" << endl;
        }
    }
    cout << endl;
}

void FindNodeByRange(linkedList List, int nilaiAwal, int nilaiAkhir)
{
    /* I.S. list mungkin kosong
       F.S. menampilkan semua node yang memiliki umur di dalam rentang nilaiAwal sampai nilaiAkhir */
    if (isEmpty(List) == true)
    {
        cout << "List kosong!" << endl;
    }
    else
    {
        address nodeBantu = List.first;
        int posisi = 0;
        bool found = false;
        cout << "--- Data diatas usia " << nilaiAwal << " dan dibawah " << nilaiAkhir << " ---" << endl;
        while (nodeBantu != Nil)
        {
            posisi++;
            int umur = nodeBantu->isidata.umur;
            if (umur > nilaiAwal && umur < nilaiAkhir)
            {
                cout << "Data ditemukan pada posisi ke-" << posisi << " :" << endl;
                cout << "Nama : " << nodeBantu->isidata.nama << ", NIM : " << nodeBantu->isidata.nim << ", Usia : " << nodeBantu->isidata.umur << endl;
                found = true;
            }
            nodeBantu = nodeBantu->next;
        }
        if (found == false)
        {
            cout << "Tidak ada data node dalam range tersebut!" << endl;
        }
    }
    cout << endl;
}
```

### main.cpp
```C++
#include <iostream>
#include "list.h"
using namespace std;

int main()
{
    linkedList List;
    address nodeA, nodeB, nodeC, nodeD, nodeE = Nil;
    createList(List);

    nodeA = alokasi("Dhimas", "2311102151", 20);
    nodeB = alokasi("Arvin", "2211110014", 21);
    nodeC = alokasi("Rizal", "2311110029", 20);
    nodeD = alokasi("Satrio", "2211102173", 21);
    nodeE = alokasi("Joshua", "2311102133", 21);

    insertFirst(List, nodeA);
    insertLast(List, nodeB);
    insertAfter(List, nodeC, nodeA);
    insertAfter(List, nodeD, nodeC);
    insertLast(List, nodeE);

    cout << "--- ISI LIST SETELAH DILAKUKAN INSERT ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    // updateFirst(List);
    // cout << "--- ISI LIST SETELAH DILAKUKAN UPDATE FIRST ---" << endl;
    // printList(List);

    // updateLast(List);
    // cout << "--- ISI LIST SETELAH DILAKUKAN UPDATE LAST ---" << endl;
    // printList(List);

    updateAfter(List, nodeC);
    cout << "--- ISI LIST SETELAH DILAKUKAN UPDATE AFTER ---" << endl;
    printList(List);

    updateByNim(List, "2211110014");
    cout << "--- ISI LIST SETELAH DILAKUKAN UPDATE BY NIM ---" << endl;
    printList(List);

    return 0;
}
```

penjelasan singkat guided 1 : Kode ini tujuannya mendemokan cara kerja linked list dengan data mahasiswa. Di main.cpp, lima node dibuat (Dhimas, Arvin, Rizal, Satrio, Joshua), lalu dimasukkan ke list dengan urutan insert yang berbeda-beda. Dhimas dimasukkan di awal, Arvin di akhir, Rizal disisipkan setelah Dhimas, Satrio setelah Rizal, dan Joshua di akhir. Jadi urutan akhir list-nya Dhimas, Rizal, Satrio, Arvin, Joshua. List itu lalu ditampilkan dengan printList beserta jumlah node-nya, yaitu 5.

Setelah itu program mencoba dua operasi update. updateAfter mengubah node yang ada setelah Rizal, yaitu Satrio. Program meminta kamu mengetik nama, NIM, dan umur baru lewat keyboard, lalu data Satrio diganti dengan data itu dan list ditampilkan lagi. Operasi kedua, updateByNim, mencari mahasiswa dengan NIM 2211110014 (Arvin) lalu mengubah datanya. Di kode ini program menampilkan “Data 2211110014 ditemukan pada posisi ke-4” beserta data Arvin, tapi data Arvin tidak benar-benar berubah. Penyebabnya, fungsi FindNodebyData selalu mengembalikan Nil walaupun datanya ketemu, jadi updateByNim mengira node-nya tidak ada dan tidak meminta input.

## UNGUIDED
### Unguided 1
### 1. BBuatlah ADT Singly Linked list di dalam file “Singlylist.h” dengan type infotype (int), address (pointer ke ElmList), ElmList (info dan next), List (first), serta primitif CreateList, alokasi, dealokasi, printInfo, dan insertFirst. Buat implementasinya pada file “Singlylist.cpp” lalu cobalah pada file “main.cpp”.

### singlylist.h
```C++
#ifndef SINGLYLIST_H_INCLUDED
#define SINGLYLIST_H_INCLUDED

#include <iostream>
#include <cstddef>

#define Nil NULL
using namespace std;

typedef int infotype;
typedef struct ElmList *address;

struct ElmList {
    infotype info;
    address next;
};

struct List {
    address first;
};

void CreateList(List &L);
address alokasi(infotype x);
void dealokasi(address &P);
void printInfo(List L);
void insertFirst(List &L, address P);
address findElm(List L, infotype x);
int totalInfo(List L);

#endif 
```
### singlylist.cpp
```C++
#include <iostream>
#include "Singlylist.h"
using namespace std;

void CreateList(List &L){
    L.first = Nil;
}

address alokasi(infotype x){
    address P = new ElmList;
    P->info = x;
    P->next = Nil;
    return P;
}

void dealokasi(address &P){
    delete P;
    P = Nil;
}

void printInfo(List L){
    address P = L.first;
    while (P != Nil) {
        cout << P->info << " ";
        P = P->next;
    }
    cout << endl;
}

void insertFirst(List &L, address P){
    P->next = L.first;
    L.first = P;
}

address findElm(List L, infotype x){
    address P = L.first;
    while (P != Nil) {
        if (P->info == x) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}

int totalInfo(List L){
    int total = 0;
    address P = L.first;
    while (P != Nil) {
        total = total + P->info;
        P = P->next;
    }
    return total;
}
```
### singlylist_main.cpp
```C++
#include <iostream>
#include "Singlylist.h"
using namespace std;

int main()
{
    List L;
    address P1, P2, P3, P4, P5 = Nil;
    address cari;

    CreateList(L);

    P1 = alokasi(2);
    insertFirst(L, P1);
    P2 = alokasi(0);
    insertFirst(L, P2);
    P3 = alokasi(8);
    insertFirst(L, P3);
    P4 = alokasi(12);
    insertFirst(L, P4);
    P5 = alokasi(9);
    insertFirst(L, P5);

    cout << "isi linked list : ";
    printInfo(L);

    cari = findElm(L, 8);
    if (cari != Nil) {
        cout << cari->info << " ditemukan dalam list" << endl;
    } else {
        cout << "8 tidak ditemukan dalam list" << endl;
    }

    cout << "Total info dari kelima elemen adalah " << totalInfo(L) << endl;

    return 0;
}
```

### Output Unguided 1 :

##### Output 1
![Screenshot Output Unguided 1_1](https://github.com/raysarahma/109082500167_Struktur-Data_Modul05/blob/main/output/output-unguide01.png)

penjelasan unguided 1 : Di unguided 1 ADT Singly Linked List dibuat dalam tiga file. Singlylist.h berisi type infotype, address, ElmList, dan List serta prototipe primitifnya, Singlylist.cpp berisi realisasinya, dan main.cpp jadi driver buat mencobanya. CreateList mengisi first dengan Nil supaya list kosong di awal. alokasi membuat elemen baru dengan new, lalu info diisi x dan next diisi Nil. insertFirst menyambungkan elemen baru ke elemen yang tadinya paling depan (P->next = L.first), lalu elemen baru itu dijadikan first. printInfo menelusuri list dari first sampai Nil sambil mencetak info tiap elemen.
Elemen 2, 0, 8, 12, 9 semuanya dimasukkan pakai insertFirst, jadi elemen yang masuk paling akhir akan ada di paling depan. Makanya outputnya 9 12 8 0 2, kebalik dari urutan input.

## Unguided 2
### 2. Carilah elemen dengan info 8 dengan membuat fungsi baru.

### singlylist.h (tambahan)
```C++
address findElm(List L, infotype x);
```
### singlylist.cpp (tambahan)
```C++
address findElm(List L, infotype x){
    address P = L.first;
    while (P != Nil) {
        if (P->info == x) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}
```
### singlylist_main.cpp (tambahan)
```C++
cari = findElm(L, 8);
if (cari != Nil) {
    cout << cari->info << " ditemukan dalam list" << endl;
} else {
    cout << "8 tidak ditemukan dalam list" << endl;
}
```
penjelasan unguided 2 : Di unguided 2 saya menambahkan fungsi findElm ke Singlylist.h dan Singlylist.cpp. Fungsi ini membuat pointer P yang mulai dari first, lalu list ditelusuri dengan while sampai P sama dengan Nil. Di setiap elemen, P->info dibandingkan dengan x. Kalau sama, alamat elemen itu langsung dikembalikan dengan return P, dan kalau sampai akhir list belum ketemu, fungsi mengembalikan Nil.

Di main.cpp hasil pencarian disimpan di variabel cari, lalu dicek dengan if. Karena list berisi 9 12 8 0 2, elemen 8 ada di list sehingga cari tidak sama dengan Nil, dan program mencetak "8 ditemukan dalam list". Kalau nilai yang dicari tidak ada, yang tercetak adalah pesan tidak ditemukan. Pengecekan Nil ini perlu supaya program tidak mengakses pointer kosong.

## Unguided 3
### 3.  Hitunglah jumlah total info seluruh elemen (9+12+8+0+2=31).
### singlylist.h (tambahan)
```C++
int totalInfo(List L);
```
### singlylist.cpp (tambahan)
```C++
int totalInfo(List L){
    int total = 0;
    address P = L.first;
    while (P != Nil) {
        total = total + P->info;
        P = P->next;
    }
    return total;
}
```
### singlylist_main.cpp
```C++
cout << "Total info dari kelima elemen adalah " << totalInfo(L) << endl;
```
penjelasan unguided 3 : Di unguided 3 saya membuat fungsi totalInfo buat menjumlahkan info semua elemen. Variabel total diisi 0 di awal, lalu list ditelusuri dari first sampai Nil dan di setiap elemen P->info ditambahkan ke total. Untuk list 9 12 8 0 2, hitungannya 0 + 9 + 12 + 8 + 0 + 2 sehingga hasilnya 31, sama dengan yang diminta di soal. Nilai total dikembalikan oleh fungsi dan dicetak di main.cpp. Cara menelusurinya sama persis dengan printInfo dan findElm, bedanya hanya apa yang dilakukan di tiap elemen.

## Kesimpulan
Dari praktikum modul 5 ini saya belajar bahwa searching pada singly linked list dilakukan dengan menelusuri node dari first sampai node yang dicari ketemu atau sampai Nil, karena list hanya bisa dibaca satu arah. Hasil pencariannya berupa alamat elemen, dan alamat itu bisa dipakai buat operasi lain seperti findBefore, delAfter, dan delP. Saya juga jadi paham operasi proses list seperti delAll, invertList, copyList, dan fCopyList, terutama bedanya copyList yang cuma menyalin alamat first (elemennya dipakai bersama) dengan fCopyList yang membuat elemen baru sehingga list hasilnya terpisah. Di bagian unguided, pola penelusuran list yang sama ternyata bisa dipakai untuk mencari elemen maupun menghitung total info semua elemen. Selain itu, pemisahan ADT ke file .h, .cpp, dan main.cpp bikin kode lebih rapi dan primitifnya bisa dipakai ulang.

## Referensi
[1] Telkom University. (2023). Modul 5 Singly Linked List (Bagian Kedua), Modul Praktikum Struktur Data. Fakultas Informatika, Telkom University.<br/> 
[2] Rahman, S., Hartono, Joni, Dafitri, H., Tanjung, J. P., & Chiuloto, K. (2025). Struktur Data dan Algoritma: Pendekatan Visual dan Praktis dalam C++ (Bab II Single Linked List). Medan: Universitas Medan Area Press. Diakses melalui https://phki-pghc.uma.ac.id/wp-content/uploads/2025/06/web-8.pdf.<br/> 
[3] cppreference.com. (n.d.). New expression dan Delete expression. Diakses melalui https://en.cppreference.com/w/cpp/language/new dan https://en.cppreference.com/w/cpp/language/delete.<br/>