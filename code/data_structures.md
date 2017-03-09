# Data Structures

## Stack

Last In, First Out. Korisno za depth-first prolaz kroz stablo

## Queue

First in, First Out. Korisno za jobove koje obavljaju više workera, ili za breadth-first prolaz kroz stablo.

## Linked List

Svaki element sadrži pointer na prethodni i sljedeći element. Korisno ako trebaš dodavati ili brisati elemente iz sredine liste, što je za array listu skuplje.

## Bloom Filter

Set koji uvijek točno kaže da se element ne nalazi u njemu, ali može imati false positive. Prednost je što zauzima znatno manje mjesta nego standardni hash set. Potrebno mu je 10 bitova po elementu

## Symmetric Delete

Omogućuje pronalaženje stringova s Levenstienovom udaljenosti u konstantnom vremenu. Korisno za search s pogreškama u tipkanju.

## Trie

Stablo koje omogućuje dohvaćanje stringova po prefixu linearno samo po duljini stringa, neovisno o broju elemenata.

## Heap

Dinamička lista koja se održava sortiranom. Dodavanje elementa u `O(log n)`. Brisanje najvećeg u `O(log n)`, samo dohvaćanje u `O(1)`.

Fibonacci Heap je nadogradnja koja dodaje elemente u `O(1)`.


