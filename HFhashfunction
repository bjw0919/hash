/*************************************
* HF(IV,M) 해쉬함수 소스 코드
*************************************/
#include <stdio.h>
// DES의 확산함수 E-Table
const unsigned int des_E[48] = { 32, 1, 2, 3, 4, 5, 4, 5, 6, 7, 8, 9, 8, 9, 10, 11, 12, 13, 12, 13, 14, 15, 16, 17,
16, 17, 18, 19, 20, 21, 20, 21, 22, 23, 24, 25, 24, 25, 26, 27, 28, 29, 28, 29, 30, 31, 32, 1, };
// DES의 순열함수 P-Table
const unsigned int des_P[32] = { 16, 7, 20, 21, 29, 12, 28, 17, 1, 15, 23, 26, 5, 18, 31, 10, 2, 8, 24, 14, 32, 27,
3, 9, 19, 13, 30, 6, 22, 11, 4, 25 };
// DES의 Sbox Table ([8]: Sbox 번호, [4]: 입력 6비트의 첫 번째와 마지막 비트, [16]: 입력의 나머지 비트)
const unsigned int des_SBOX[8][4][16] = {
/*S1*/ { { 14, 4, 13, 1, 2, 15, 11, 8, 3, 10, 6, 12, 5, 9, 0, 7 }, { 0, 15, 7, 4, 14, 2, 13, 1, 10, 6, 12, 11, 9, 5, 3,
8 }, { 4, 1, 14, 8, 13, 6, 2, 11, 15, 12, 9, 7, 3, 10, 5, 0 }, { 15, 12, 8, 2, 4, 9, 1, 7, 5, 11, 3, 14, 10, 0, 6, 13 }
},
/*S2*/ { { 15, 1, 8, 14, 6, 11, 3, 4, 9, 7, 2, 13, 12, 0, 5, 10 }, { 3, 13, 4, 7, 15, 2, 8, 14, 12, 0, 1, 10, 6, 9, 11,
5 }, { 0, 14, 7, 11, 10, 4, 13, 1, 5, 8, 12, 6, 9, 3, 2, 15 }, { 13, 8, 10, 1, 3, 15, 4, 2, 11, 6, 7, 12, 0, 5, 14, 9 }
},
/*S3*/ { { 10, 0, 9, 14, 6, 3, 15, 5, 1, 13, 12, 7, 11, 4, 2, 8 }, { 13, 7, 0, 9, 3, 4, 6, 10, 2, 8, 5, 14, 12, 11, 15,
1 }, { 13, 6, 4, 9, 8, 15, 3, 0, 11, 1, 2, 12, 5, 10, 14, 7 }, { 1, 10, 13, 0, 6, 9, 8, 7, 4, 15, 14, 3, 11, 5, 2, 12 }
},
/*S4*/ { { 7, 13, 14, 3, 0, 6, 9, 10, 1, 2, 8, 5, 11, 12, 4, 15 }, { 13, 8, 11, 5, 6, 15, 0, 3, 4, 7, 2, 12, 1, 10, 14,
9 }, { 10, 6, 9, 0, 12, 11, 7, 13, 15, 1, 3, 14, 5, 2, 8, 4 }, { 3, 15, 0, 6, 10, 1, 13, 8, 9, 4, 5, 11, 12, 7, 2, 14 }
},
/*S5*/ { { 2, 12, 4, 1, 7, 10, 11, 6, 8, 5, 3, 15, 13, 0, 14, 9 }, { 14, 11, 2, 12, 4, 7, 13, 1, 5, 0, 15, 10, 3, 9, 8,
6 }, { 4, 2, 1, 11, 10, 13, 7, 8, 15, 9, 12, 5, 6, 3, 0, 14 }, { 11, 8, 12, 7, 1, 14, 2, 13, 6, 15, 0, 9, 10, 4, 5, 3 }
},
/*S6*/ { { 12, 1, 10, 15, 9, 2, 6, 8, 0, 13, 3, 4, 14, 7, 5, 11 }, { 10, 15, 4, 2, 7, 12, 9, 5, 6, 1, 13, 14, 0, 11, 3,
8 }, { 9, 14, 15, 5, 2, 8, 12, 3, 7, 0, 4, 10, 1, 13, 11, 6 }, { 4, 3, 2, 12, 9, 5, 15, 10, 11, 14, 1, 7, 6, 0, 8, 13 }
},
/*S7*/ { { 4, 11, 2, 14, 15, 0, 8, 13, 3, 12, 9, 7, 5, 10, 6, 1 }, { 13, 0, 11, 7, 4, 9, 1, 10, 14, 3, 5, 12, 2, 15, 8,
6 }, { 1, 4, 11, 13, 12, 3, 7, 14, 10, 15, 6, 8, 0, 5, 9, 2 }, { 6, 11, 13, 8, 1, 4, 10, 7, 9, 5, 0, 15, 14, 2, 3, 12 }
},
/*S8*/ { { 13, 2, 8, 4, 6, 15, 11, 1, 10, 9, 3, 14, 5, 0, 12, 7 }, { 1, 15, 13, 8, 10, 3, 7, 4, 12, 5, 6, 11, 0, 14, 9,
2 }, { 7, 11, 4, 1, 9, 12, 14, 2, 0, 6, 10, 13, 15, 3, 5, 8 }, { 2, 1, 14, 7, 4, 10, 8, 13, 15, 12, 9, 0, 3, 5, 6, 11 }
}
};
// DES의 bit 변환
// Table : 테이블, InSize : 테이블입력크기, OutSize : 테이블출력크기
void DES_bit_transformation(unsigned int *State, const unsigned int *Table, int InSize, int OutSize)
{
int i;
unsigned int temp[2] = { 0, };
int in = InSize >> 1;
int out = OutSize >> 1;
for (i = 0; i < OutSize; i++)
temp[i / out] ^= ((State[((Table[i] - 1) / in)] >> (((Table[i] - 1) / in + 1)*in - Table[i])) & 1) <<
(out - 1 - (i % out));
State[0] = temp[0];
State[1] = temp[1];
}
// DES의 F함수 (Expansion - 키XOR - Sbox - 순열 P)
// State : 32bit, RoundKey : 48bit
int DES_F(unsigned int State, const unsigned int *RoundKey)
{
int i;
unsigned int temp[2] = { 0, };
unsigned int temp_S[2] = { 0, };
temp[0] = State >> 16;
temp[1] = State & 0xffff;
//Expansion
DES_bit_transformation(temp, des_E, 32, 48);
//키XOR
temp[0] ^= RoundKey[0];
temp[1] ^= RoundKey[1];
//Sbox
for (i = 0; i < 4; i++, temp[0] >>= 6, temp[1] >>= 6)
{
temp_S[0] ^= (des_SBOX[3 - i][((temp[0] >> 4) & 0x2) ^ (temp[0] & 0x1)][(temp[0] >> 1) &
0xf]) << (4 * i);
temp_S[1] ^= (des_SBOX[7 - i][((temp[1] >> 4) & 0x2) ^ (temp[1] & 0x1)][(temp[1] >> 1) &
0xf]) << (4 * i);
}
//순열 P
DES_bit_transformation(temp_S, des_P, 32, 32);
return (temp_S[0] << 16) ^ temp_S[1];
}
//120bit 메시지 입력, 64bit 해쉬 결과
int DES_HASH_120bit(const unsigned int *M, unsigned int * Result)
{
//IV 벡터
unsigned int IV[2] = { 0xef926535, 0x89793238 }, Round_L, Round_R, Round_T;
unsigned int temp[2] = { 0, }; //입력 M
Round_L = IV[0];
Round_R = IV[1];
temp[0] = M[0];
temp[1] = M[0];
//M[0]||M[0]
Round_T = Round_R;
Round_R = Round_L^DES_F(Round_R, temp);
Round_L = Round_T;
temp[0] = M[1];
temp[1] = M[2];
//M[1]||M[2]
Round_T = Round_R;
Round_R = Round_L^DES_F(Round_R, temp);
Round_L = Round_T;
temp[0] = M[3];
temp[1] = M[4];
//M[3]||M[4]
Round_T = Round_R;
Round_R = Round_L^DES_F(Round_R, temp);
Round_L = Round_T;
temp[0] = M[0];
temp[1] = M[0];
//M[0]||M[0]
Round_L = Round_L^DES_F(Round_R, temp);
*Result = Round_L;
*(Result + 1) = Round_R;
return 1;
}
int main()
{
int i;
unsigned int message[3][5] = {
{ 1, 2, 3, 4, 5 },
{ 6, 7, 8, 9, 0 },
{ 11, 22, 33, 44, 55 }
};
unsigned int Result[3][2];
for (i = 0; i < 3; i++)
{
printf("메세지: %08x,%08x,%08x,%08x,%08x\n", message[i][0],
message[i][1], message[i][2], message[i][3], message[i][4]);
DES_HASH_120bit(message[i], Result[i]);
printf("해쉬 결과: %08x, %08x\n", Result[i][0], Result[i][1]);
}
return 0;
}
