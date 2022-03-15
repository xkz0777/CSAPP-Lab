```assembly
00000000004010f4 <phase_6>:
  4010f4:	41 56                	push   %r14
  4010f6:	41 55                	push   %r13
  4010f8:	41 54                	push   %r12
  4010fa:	55                   	push   %rbp
  4010fb:	53                   	push   %rbx
  
  # read
  4010fc:	48 83 ec 50          	sub    $0x50,%rsp # allocate 80 bytes
  401100:	49 89 e5             	mov    %rsp,%r13 # r13 = rsp
  401103:	48 89 e6             	mov    %rsp,%rsi # rsi = rsp
  401106:	e8 51 03 00 00       	callq  40145c <read_six_numbers> # six item array, denote as a
  40110b:	49 89 e6             	mov    %rsp,%r14 # r14 = rsp
  40110e:	41 bc 00 00 00 00    	mov    $0x0,%r12d # r12d = 0
  
  # outer
  401114:	4c 89 ed             	mov    %r13,%rbp # rbp = r13
  401117:	41 8b 45 00          	mov    0x0(%r13),%eax # eax = *r13 = a[r12d]
  40111b:	83 e8 01             	sub    $0x1,%eax # eax--
  40111e:	83 f8 05             	cmp    $0x5,%eax # eax:5
  # every item in a is between 1 and 6
  401121:	76 05                	jbe    401128 <phase_6+0x34>
  # 0 <= eax <= 5, skip next
  401123:	e8 12 03 00 00       	callq  40143a <explode_bomb>
  401128:	41 83 c4 01          	add    $0x1,%r12d # r12d++
  40112c:	41 83 fc 06          	cmp    $0x6,%r12d # r12d:6
  401130:	74 21                	je     401153 <phase_6+0x5f> # r12d == 6, break outer
  401132:	44 89 e3             	mov    %r12d,%ebx # ebx = r12d
  
  # inner, ensures that the rest items in the array != r12dth item
  401135:	48 63 c3             	movslq %ebx,%rax
  401138:	8b 04 84             	mov    (%rsp,%rax,4),%eax # eax = *(rsp + 4 * rax) = a[rax]
  40113b:	39 45 00             	cmp    %eax,0x0(%rbp)
  40113e:	75 05                	jne    401145 <phase_6+0x51> # *rbp != eax, skip next
  401140:	e8 f5 02 00 00       	callq  40143a <explode_bomb>
  401145:	83 c3 01             	add    $0x1,%ebx # ebx++
  401148:	83 fb 05             	cmp    $0x5,%ebx # ebx:5
  40114b:	7e e8                	jle    401135 <phase_6+0x41> # inner
  40114d:	49 83 c5 04          	add    $0x4,%r13 # r13 += 4
  401151:	eb c1                	jmp    401114 <phase_6+0x20> # outer
  
  401153:	48 8d 74 24 18       	lea    0x18(%rsp),%rsi # rsi = rsp + 24
  401158:	4c 89 f0             	mov    %r14,%rax # rax = r14 = rsp
  40115b:	b9 07 00 00 00       	mov    $0x7,%ecx # ecx = 7
  
  # loop, for i in range(0, 6): a[i] = 7 - a[i] 
  401160:	89 ca                	mov    %ecx,%edx # edx = ecx = 7
  401162:	2b 10                	sub    (%rax),%edx # edx -= *rax
  401164:	89 10                	mov    %edx,(%rax) # *rax = edx
  401166:	48 83 c0 04          	add    $0x4,%rax # rax += 4
  40116a:	48 39 f0             	cmp    %rsi,%rax # rax:rsi
  40116d:	75 f1                	jne    401160 <phase_6+0x6c> # loop
  
  40116f:	be 00 00 00 00       	mov    $0x0,%esi # esi = 0
  401174:	eb 21                	jmp    401197 <phase_6+0xa3> # to label
  
  # outer, write 6 long int to array b start at address (rsp + 2 * rsi + 32)
  # if a[i] = 1, b[i] = 0x6032d0, otherwise, b[i] = 0x6032d0 + a[i] * 0x10
  401176:	48 8b 52 08          	mov    0x8(%rdx),%rdx # rdx = *(rdx + 8)
  40117a:	83 c0 01             	add    $0x1,%eax # eax++
  40117d:	39 c8                	cmp    %ecx,%eax # eax:ecx
  40117f:	75 f5                	jne    401176 <phase_6+0x82> # outer
  
  401181:	eb 05                	jmp    401188 <phase_6+0x94> # skip next
  
  # inner
  401183:	ba d0 32 60 00       	mov    $0x6032d0,%edx # edx = 0x6032d0
  401188:	48 89 54 74 20       	mov    %rdx,0x20(%rsp,%rsi,2) # *(rsp + 2 * rsi + 32) = rdx
  40118d:	48 83 c6 04          	add    $0x4,%rsi # rsi += 4
  401191:	48 83 fe 18          	cmp    $0x18,%rsi # rsi:24
  401195:	74 14                	je     4011ab <phase_6+0xb7> # if rsi = 24, break
  
  # label
  401197:	8b 0c 34             	mov    (%rsp,%rsi,1),%ecx # ecx = *(rsp + rsi)
  40119a:	83 f9 01             	cmp    $0x1,%ecx # ecx:1
  40119d:	7e e4                	jle    401183 <phase_6+0x8f> # if ecx == 1, inner
  40119f:	b8 01 00 00 00       	mov    $0x1,%eax # eax = 1
  4011a4:	ba d0 32 60 00       	mov    $0x6032d0,%edx # edx = 0x6032d0
  4011a9:	eb cb                	jmp    401176 <phase_6+0x82> # outer
  
  # for j in range(1, 6):
  # 	*(b[j - 1] + 8) = b[j]
  4011ab:	48 8b 5c 24 20       	mov    0x20(%rsp),%rbx # rbx = *(rsp + 32) = b[0]
  4011b0:	48 8d 44 24 28       	lea    0x28(%rsp),%rax # rax = rsp + 40, points to b[1]
  4011b5:	48 8d 74 24 50       	lea    0x50(%rsp),%rsi # rsi = rsp + 80, end
  4011ba:	48 89 d9             	mov    %rbx,%rcx # rcx = rbx
  4011bd:	48 8b 10             	mov    (%rax),%rdx # rdx = *(rax) = b[j]
  4011c0:	48 89 51 08          	mov    %rdx,0x8(%rcx) # *(rcx + 8) = rdx
  4011c4:	48 83 c0 08          	add    $0x8,%rax # rax += 8
  4011c8:	48 39 f0             	cmp    %rsi,%rax # rax:rsi
  4011cb:	74 05                	je     4011d2 <phase_6+0xde> 
  4011cd:	48 89 d1             	mov    %rdx,%rcx # rcx = rdx
  4011d0:	eb eb                	jmp    4011bd <phase_6+0xc9>
  
  4011d2:	48 c7 42 08 00 00 00 	movq   $0x0,0x8(%rdx) # *(rdx + 8) = 0
  4011d9:	00 
  
  
  4011da:	bd 05 00 00 00       	mov    $0x5,%ebp # ebp = 5
  
  # loop
  4011df:	48 8b 43 08          	mov    0x8(%rbx),%rax # rax = *(rbx + 8)
  4011e3:	8b 00                	mov    (%rax),%eax # eax = *rax
  4011e5:	39 03                	cmp    %eax,(%rbx) # *rbx:eax
  4011e7:	7d 05                	jge    4011ee <phase_6+0xfa> # *rbx>=eax, skip next
  4011e9:	e8 4c 02 00 00       	callq  40143a <explode_bomb>
  4011ee:	48 8b 5b 08          	mov    0x8(%rbx),%rbx # rbx = *(rbx + 8)
  4011f2:	83 ed 01             	sub    $0x1,%ebp # ebp--
  4011f5:	75 e8                	jne    4011df <phase_6+0xeb> # ebp != 0 loop
  
  4011f7:	48 83 c4 50          	add    $0x50,%rsp
  4011fb:	5b                   	pop    %rbx
  4011fc:	5d                   	pop    %rbp
  4011fd:	41 5c                	pop    %r12
  4011ff:	41 5d                	pop    %r13
  401201:	41 5e                	pop    %r14
  401203:	c3                   	retq
```

