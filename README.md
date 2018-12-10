# 模拟实现动态内存分区分配算法
该项目是在Linux环境下下用C++实现的四种动态分区分配算法(首次适应、循环首次适应、最佳适应、最坏适应算法)
环境：Debian 7.3.0-19
编译器：gcc
程序思想：
  两个类：
  　　一个是内存块类，存放每一块内存的起始地址、结束地址和大小,提供内存块信息的设置、获取、内存块分割的接口。（memNode.h）
  　　另一个是操作系统类，存放保存内存块信息的两个链表、最小不可分割内存块大小和初始内存块个数信息，提供这几种分配算法的公有接口以及实现算法的若干成员函数。(os.h)

　程序启动时从键盘接收内存的最小不可分割大小minSize和初始内存块数N，然后从键盘接收N个内存块的起始地址和结束地址。
　使用两个链表来存储内存块的信息，一个是空闲内存块链表（_empty），另一个是已分配内存块链表(_use)。（使用STL库中的list实现）
　内存块信息输入完成后就可以选择内存的分配算法，然后就可以选择要分配内存还是释放内存。
　　　分配内存：需要输入需要的内存大小，然后按照所选的算法来进行分配（从_empty链表切割或者摘下一个内存块，挂到_use链表中）。
　　　释放内存：需要输入要释放的内存块起始地址，然后根据地址在_use链表中找到对应的内存块挂回到_empty链表中，再对_empty链表按照起始地址升序排序后进行遍历，看有没有可以合并的内存块。合并检查完成后按照算法要求将_empty链表进行排序，以便下一次分配。
  
  
动态分区分配是根据进程的实际需要，动态地为之分配内存空间。在实现可变分区分配时，将涉及到分区分配中所用的数据结构、分区分配算法和分区的分配与回收操作这样三个问题。

1.首次适应算法(First Fit)
  FF算法要求空闲分区链以地址递增的次序链接。
  在分配内存时，从链首开始顺序查找，直至找到一个大小能满足要求的空闲分区为止；
  然后再按照作业的大小，从该分区中划出一块内存空间分配给请求者，余下的空闲分区仍留在空闲链中。
  若从链首直至链尾都不能找到一个能满足要求的分区，则此次内存分配失败，返回。
  首次适应算法倾向于优先利用内存中低址部分的空闲分区，从而保留了高址部分的大空闲区。这给为以后到达的大作业分配大的内存空间创造了条件。其缺点是低址部分不断被划分，会留下许多难以利用的、很小的空闲分区，而每次查找又都是从低址部分开始，这无疑会增加查找可用空闲分区时的开销。
2.循环首次适应算法(Next Fit)
  该算法是由首次适应算法演变而成的。在为进程分配内存空间时，不再是每次都从链首开始查找，而是从上次找到的空闲分区的下一个空闲分区开始查找，直至找到一个能满足要求的空闲分区，从中划出一块与请求大小相等的内存空间分配给作业。
  该算法能使内存中的空闲分区分布得更均匀，从而减少了查找空闲分区时的开销，但这样会缺乏大的空闲分区。 
3.最佳适应算法(Best Fit)
  所谓“最佳”是指每次为作业分配内存时，总是把能满足要求、又是最小的空闲分区分配给作业，避免“大材小用”。为了加速寻找，该算法要求将所有的空闲分区按其容量以从小到大的顺序形成一空闲分区链。这样，第一次找到的能满足要求的空闲区，必然是最佳的。
  孤立地看，最佳适应算法似乎是最佳的，然而在宏观上却不一定。因为每次分配后所切割下来的剩余部分总是最小的，这样，在存储器中会留下许多难以利用的小空闲区。 
4.最坏适应算法(Worst Fit)
  最坏适应分配算法要扫描整个空闲分区表或链表，总是挑选一个最大的空闲区分割给作业使用，其优点是可使剩下的空闲区不至于太小，产生碎片的几率最小，对中、小作业有利，同时最坏适应分配算法查找效率很高。该算法要求将所有的空闲分区按其容量以从大到小的顺序形成一空闲分区链，查找时只要看第一个分区能否满足作业要求。
  但是该算法的缺点也很明显的，它会使存储器中缺乏大的空闲分区。