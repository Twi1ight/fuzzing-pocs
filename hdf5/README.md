# HDF5-1.10.2 vulnerabilities

HDF5 is a data model, library, and file format for storing and managing data. 

Download link: [https://portal.hdfgroup.org/display/support/Downloads](https://portal.hdfgroup.org/display/support/Downloads)

All testcase run with h5dump

### Null pointer dereference in H5O__chunk_deserialize

```
(gdb) r H5O__chunk_deserialize-H5Ocache.c_1566-null_point_dereference 
Starting program: /opt/test/h5dump H5O__chunk_deserialize-H5Ocache.c_1566-null_point_dereference

Program received signal SIGSEGV, Segmentation fault.
H5O__chunk_deserialize (oh=oh@entry=0x975210, addr=<optimized out>, len=<optimized out>, image=image@entry=0x975000 "\001", udata=udata@entry=0x7fffffffd0c0, dirty=dirty@entry=0x7fffffffcf9b)
    at /opt/hdf5-1.10.2/src/H5Ocache.c:1566
1566	                oh->nlink = *refcount;
(gdb) p refcount
$1 = (H5O_refcount_t *) 0x0
```

### Division by zero in H5D__chunk_init

```
(gdb) r H5D__chunk_init-H5Dchunk.c_1022-div_by_zero
Program received signal SIGFPE, Arithmetic exception.
0x0000000000465691 in H5D__chunk_init (f=0x971730, dxpl_id=720575940379279368, dset=0x97d1f0, dapl_id=<optimized out>) at /opt/hdf5-1.10.2/src/H5Dchunk.c:1022
1022	            rdcc->scaled_dims[u] = dset->shared->curr_dims[u] / dset->shared->layout.u.chunk.dim[u];
(gdb) x/i $pc
=> 0x465691 <H5D__chunk_init+321>:	div    %rcx
(gdb) i r
rax            0x14	20
rbx            0x97d1f0	9949680
rcx            0x0	0
rdx            0x0	0
```

### Null pointer dereference in H5S_hyper_make_spans

```
(gdb) r H5S_hyper_make_spans-H5Shyper.c_6139-null_pointer_dereference 
Starting program: /opt/test/h5dump H5S_hyper_make_spans-H5Shyper.c_6139-null_pointer_dereference
HDF5 "H5S_hyper_make_spans-H5Shyper.c_6139-null_pointer_dereference" {
GROUP "/" {
   DATASET "Compressed_Data" {
      DATATYPE  H5T_STD_I32BE
      DATASPACE  SCALAR

Program received signal SIGSEGV, Segmentation fault.
H5S_hyper_make_spans (block=0x7fffffff98d0, count=0x7fffffff97c0, stride=0x7fffffff96b0, start=0x7fffffff95a0, rank=0) at /opt/hdf5-1.10.2/src/H5Shyper.c:6139
6139	    down->count = 1;
(gdb) p down
$2 = (H5S_hyper_span_info_t *) 0x0
```

### Division by zero in H5D__btree_decode_key

```
(gdb) r H5D__btree_decode_key-H5Dbtree.c_697-div_by_zero 
Starting program: /opt/test/h5dump H5D__btree_decode_key-H5Dbtree.c_697-div_by_zero
Program received signal SIGFPE, Arithmetic exception.
0x0000000000646ac5 in H5D__btree_decode_key (shared=<optimized out>, raw=0x978da8 "\024", _key=0x97a928) at /opt/hdf5-1.10.2/src/H5Dbtree.c:697
697	        key->scaled[u] = tmp_offset / layout->dim[u];
(gdb) p layout->dim[u]
value has been optimized out
(gdb) x/i $pc
=> 0x646ac5 <H5D__btree_decode_key+229>:	div    %rcx
(gdb) i r
rax            0x100000640	4294968896
rbx            0x97a7e0	9938912
rcx            0x0	0
rdx            0x0	0
```

### Out of bound read in H5VM_memcpyvv

```
(gdb) r H5VM_memcpyvv-H5VM.c_1626-oob_read
Starting program: /opt/test/h5dump H5VM_memcpyvv-H5VM.c_1626-oob_read
HDF5 "H5VM_memcpyvv-H5VM.c_1626-oob_read" {
GROUP "/" {
   DATASET "Compressed_Data" {
      DATATYPE  H5T_STD_I32BE
      DATASPACE  SIMPLE { ( 100, 20 ) / ( 100, 20 ) }

Program received signal SIGSEGV, Segmentation fault.
__memcpy_sse2_unaligned () at ../sysdeps/x86_64/multiarch/memcpy-sse2-unaligned.S:35
35	../sysdeps/x86_64/multiarch/memcpy-sse2-unaligned.S: No such file or directory.
(gdb) bt
#0  __memcpy_sse2_unaligned () at ../sysdeps/x86_64/multiarch/memcpy-sse2-unaligned.S:35
#1  0x0000000000627a1f in memcpy (__len=80, __src=<optimized out>, __dest=<optimized out>) at /usr/include/x86_64-linux-gnu/bits/string3.h:53
#2  H5VM_memcpyvv (_dst=0x7ffff7e68018, dst_max_nseq=<optimized out>, dst_curr_seq=dst_curr_seq@entry=0x7fffffffa5c8, dst_len_arr=dst_len_arr@entry=0x7fffffffa5d8, 
    dst_off_arr=dst_off_arr@entry=0x7fffffffa5c0, _src=0x995440, src_max_nseq=20, src_curr_seq=0x7fffffffa5d0, src_len_arr=0x98eac8, src_off_arr=0x990af8) at /opt/hdf5-1.10.2/src/H5VM.c:1626
......
(gdb) f 2
#2  H5VM_memcpyvv (_dst=0x7ffff7e68018, dst_max_nseq=<optimized out>, dst_curr_seq=dst_curr_seq@entry=0x7fffffffa5f8, dst_len_arr=dst_len_arr@entry=0x7fffffffa608, 
    dst_off_arr=dst_off_arr@entry=0x7fffffffa5f0, _src=0x9953c0, src_max_nseq=20, src_curr_seq=0x7fffffffa600, src_len_arr=0x98ea48, src_off_arr=0x990a78) at /opt/csn/hdf5-1.10.2/src/H5VM.c:1626
1626	            HDmemcpy(dst, src, tmp_src_len);
(gdb) p src
$22 = <optimized out>
```

As we can see, the src is optimized out, so we check the source code, and found the src is computed:

```c
1613	    /* Compute buffer offsets */
1614	    dst = (unsigned char *)_dst + *dst_off_ptr;
1615	    src = (const unsigned char *)_src + *src_off_ptr;  // <------ src
1616	
1617	/* Work through the sequences */
1618	/* (Choose smallest sequence available initially) */
1619	
1620	    /* Source sequence is less than destination sequence */
1621	    if(tmp_src_len < tmp_dst_len) {
1622	src_smaller:
1623	        acc_len = 0;
1624	        do {
1625	            /* Copy data */
1626	            HDmemcpy(dst, src, tmp_src_len);  //<-------- crash here
```
back to gdb, we could see the out of bound read
```
(gdb) p _src
$23 = (const void *) 0x9953c0
(gdb) p *src_off_ptr
$24 = 131152
(gdb) p (const unsigned char *)_src + *src_off_ptr
$25 = (const unsigned char *) 0x9b5410 <error: Cannot access memory at address 0x9b5410>
```

### Out of bound read in H5O_fill_new_decode and H5O_fill_old_decode

```
(gdb) r H5O_fill_new_decode-H5Ofill.c_233-oob_read
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /opt/test/h5dump H5O_fill_new_decode-H5Ofill.c_233-oob_read

Program received signal SIGSEGV, Segmentation fault.
__memcpy_sse2_unaligned () at ../sysdeps/x86_64/multiarch/memcpy-sse2-unaligned.S:38
38	../sysdeps/x86_64/multiarch/memcpy-sse2-unaligned.S: No such file or directory.
(gdb) bt
#0  __memcpy_sse2_unaligned () at ../sysdeps/x86_64/multiarch/memcpy-sse2-unaligned.S:38
#1  0x000000000053285d in memcpy (__len=<optimized out>, __src=0x9753b0, __dest=<optimized out>) at /usr/include/x86_64-linux-gnu/bits/string3.h:53
#2  H5O_fill_new_decode (f=<optimized out>, dxpl_id=<optimized out>, open_oh=<optimized out>, mesg_flags=<optimized out>, ioflags=<optimized out>, p_size=<optimized out>, p=0x9753b0 "\v")
    at /opt/hdf5-1.10.2/src/H5Ofill.c:233
#3  H5O_fill_new_shared_decode (f=<optimized out>, dxpl_id=<optimized out>, open_oh=<optimized out>, mesg_flags=<optimized out>, ioflags=<optimized out>, p_size=<optimized out>, p=0x9753a8 "\002\003\002\001")
    at /opt/hdf5-1.10.2/src/H5Oshared.h:82
......
(gdb) f 2
#2  H5O_fill_new_decode (f=<optimized out>, dxpl_id=<optimized out>, open_oh=<optimized out>, mesg_flags=<optimized out>, ioflags=<optimized out>, p_size=<optimized out>, p=0x9753b0 "\v")
    at /opt/hdf5-1.10.2/src/H5Ofill.c:233
233	                HDmemcpy(fill->buf, p, (size_t)fill->size);
(gdb) p p
$5 = (const uint8_t *) 0x9753b0 "\v"
(gdb) p fill
$6 = (H5O_fill_t *) 0x97eaf0
(gdb) p *fill
$7 = {sh_loc = {type = 0, file = 0x0, msg_type_id = 0, u = {loc = {index = 0, oh_addr = 0}, heap_id = {id = "\000\000\000\000\000\000\000", val = 0}}}, version = 2, type = 0x0, size = 8388608, 
  buf = 0x7ffff6cff010, alloc_time = H5D_ALLOC_TIME_INCR, fill_time = H5D_FILL_TIME_IFSET, fill_defined = true}
(gdb) p fill->buf
$8 = (void *) 0x7ffff6cff010
(gdb) p fill->size
$9 = 8388608
(gdb) p p+fill->size
$10 = (const uint8_t *) 0x11753b0 <error: Cannot access memory at address 0x11753b0>
```

the `HDmemcpy` is just a wrapper of memcpy, the size `fill->size` is large than p's size, which caused `p+fill->size` out of bound.

this issue exist in `H5O_fill_old_decode` too, in file `H5Ofill.c` line 337

