# sys_prog_assignment
implement sorts (bubble, selection, insertion) with assembly language using sictool


START	LDA	ZERO
	STA	IDX
	STA	TEMP

INPUT	LDX	IDX	.LOOP로 입력을 구성하기 위해 INDEX 사용
	LDA	ZERO
	RD	STDIN	.터미널로부터 1글자를 읽어옴
	COMP	MAX
	JGT	END	.99999보다 입력값이 큰 경우, 종료
	COMP	EEE
	JEQ	EOFCK
	COMP	CTEN
	JLT	CHECK
	JEQ	CHECK

EOFCK	LDA	ZERO	.EOF CHECK
	RD	STDIN	.E 다음이 O가 나오는지 확인
	COMP	OOO	.맞으면 F확인
	JEQ	FCK
	J	END	.아니면 종료
FCK	RD	STDIN
	COMP	FFF	.다음이 F인지 확인
	JEQ	BUBBLE
	.JEQ	SELECT	.맞으면 sorting
	.JEQ	INSERT	
	J	END

OCHCK	COMP	OOO
	JEQ	FCHCK
	J	INPUT
FCHCK	COMP	FFF
	.JEQ	BUBBLE
CHECK	COMP	SPACE	.SPACE(공백)을 만나면
	JEQ	UPDIDX	.IDX를 update하는 code로 jump
	SUB	CONV	.character type으로 값을 받아오기 때문에 정수값으로 만들어줌
	STA	ARR,X	.배열의 X번쨰에 값 저장
	LDA	TEMP	.수가 1자리가 아닐 수 있기 때문에 TEMP값에 10을 곱하고,
	MUL	TEN	
	ADD	ARR,X	.현재 배열에 저장할 값을 TEMP값과 더하여 다시 TEMP에 저장해둠
	STA	TEMP
	J	INPUT	.LOOP 반복

UPDIDX	LDA	TEMP	.최종 TEMP에 남은 값을 불러와
	STA	ARR,X	.배열의 현재 위치에 저장
	LDA 	IDX	
	ADD	THREE	.WORD가 3byte이므로 3을 더해줌
	STA	IDX
	LDA	MAXLEN	.배열의 크기를 update
	ADD	ONE
	STA	MAXLEN
	COMP	EIGHT
	JGT	END
	J	INITT	.다음 loop에 사용할 TEMP를 초기화
INITT	LDA	ZERO
	STA	TEMP
	J	INPUT	.LOOP 반복

.BUBBLE SORT
BUBBLE	LDX	ZERO	.외부 LOOP는 0부터 시작
	LDA	MAXLEN	.배열의 길이를 WORD의 길이로 바꿔주기 위해
	MUL	THREE	.3을 곱해줌
	STA	MAXLEN
	LDA	THREE	.
	STA	IDX2	.내부 LOOP에서는 0번째가 아닌 1번째 배열의 값부터 시작하여 3을 저장
	LDA	ZERO
	STA	IDX	.외부 LOOP의 index 관리(2중 for문에서 첫번째 for문의 index  역할)
	STA	TIDX	.임시로 IDX를 저장할 공간
	STA 	TEMP	.임시로 값을 저장할 공간들(2,3까지)
	STA	TEMP2
	STA	TEMP3

BLOOP	LDA	THREE	.외부 LOOP가 한번 돌때마다 IDX2 초기화, IDX를 index register에 setting
	STA	IDX2
	LDA	ZERO
	LDX	IDX
		
INLOOP	LDX	IDX2	
	LDA	ARR,X	.현재 위치의 배열의 값을 TEMP에 저장
	STA	TEMP
	LDA	IDX2
	SUB	THREE	.배열의 앞칸의 값과 비교를 위해 IDX2에서 3을 뺸 값을 TIDX에 저장
	STA	TIDX
	LDX	TIDX	
	LDA	ARR,X	.현재 위치에서 배열의 앞칸의 값을 불러와
	COMP	TEMP	.TEMP 값과 비교
	JLT	UPDIN	.앞칸의 값보다 현재 위치의 값이 더 클 경우
	JEQ	UPDIN	.index update code로 jump
	STX	PTIDX	.Subroutine으로 가기전에 사용하는 register의 값을 PIDX와 TEMP4에 저장
	STA	TEMP4	
	JSUB	SWAP	.앞칸이 더 클 경우 자리 바꾸는 subroutine으로
	JSUB	PRINT
	LDX	PTIDX	.Subroutine에서 돌아온 후 register 값 다시 setting
	LDA	TEMP4
	
UPDIN	LDA	IDX2	.IDX2에WORD의 크기 3을 더해 다음 index로 update
	ADD	THREE
	STA	IDX2
	COMP	MAXLEN	
	JEQ	UPDOUT	.index가 배열의 길이와 같아지면 외부LOOP의 index update로 jump
	J	INLOOP	.그렇지 않을 경우 내부 LOOP 반복 진행

UPDOUT	LDA	IDX	.IDX에 WORD의 크기 3을 더해 다음 index로 update
	ADD	THREE
	STA	IDX
	COMP	MAXLEN	
	JLT	BLOOP	.index가 배열의 길이보다 작은 경우, 외부 LOOP 반복 진행
	JEQ	END	.그렇지 않을 경우, finish
	JGT	END
.BUBBLE SORT FINISH

.SELECTION SORT
SELECT	LDX	ZERO
	LDA	MAXLEN	.배열의 길이에 3을 곱해 총 WORD의 길이로 저장
	MUL	THREE
	STA	MAXLEN
	SUB	THREE
	STA	MXLNMO	.배열의 길이보다 WORD가 하나 작은 조건을 위해 저장
	LDA	ZERO
	STA	IDX	.i
	STA	MINIDX	.최소값의 index number
	STA	TEMP
	STA	TEMP2
	STA	TEMP3
	STA	TIDX
	STA	TIDX2	.값을 임시로 저장할 공간 초기화
	STA	IDX2	.j

SLOOP	LDA	IDX
	STA	MINIDX	.sorting되지 않은 첫번째 배열의 index를 저장
	ADD	THREE
	STA	IDX2	.외부LOOP의 index에 WORD 하나의 크기를 더해 다음 index를 가리키도록 함
	LDX	IDX

SILOOP	LDX	IDX2
	LDA	ARR,X	.ARR[j]의 값을 불러움
	STA	TEMP
	LDX	MINIDX
	LDA	ARR,X	.ARR[min_idx]의 값을 불러옴
	LDX	IDX2
	COMP	TEMP
	JEQ	SIIDX	.ARR[min_idx] <= ARR[j]의 경우
	JLT	SIIDX	.내부LOOP의 index를 update하는 code로 jump
	LDA	IDX2	.그렇지 않을 경우, 최소값의 index를 update
	STA	MINIDX

SIIDX	LDA	IDX2
	ADD	THREE
	STA	IDX2	.내부LOOP의 index를 update
	COMP	MAXLEN	.배열의 길이와 같아지면
	JEQ	SPUSH	.최소값의 자리를 바꿔주는 code로 jump
	J	SILOOP	.그렇지 않을 경우, LOOP 진행

SPUSH	LDX	MINIDX
	LDA	ARR,X	.배열의 최소값을 불러와 TEMP2에저장
	STA	TEMP2
	STX	TIDX	.최소값의 index를 TIDX에 저장
SWHILE	LDX	TIDX
	LDA	IDX
	COMP	MINIDX	.최소값의 index와 현재 가장 작은값이 들어갈 index가 같으면 jump
	JEQ	SJUMP
	JSUB	SISWAP	.배열값의 위치를 바꿔주는 Subroutine으로 jump
SJUMP	LDA	TIDX
	SUB	THREE
	STA	TIDX
	COMP	IDX
	JGT	SWHILE	.최소값의 index에서 3씩 줄여가다 IDX보다 작으면 LOOP 탈출
	JSUB	PRINT
	J	SOIDX

SOIDX	LDA	IDX
	ADD	THREE
	STA	IDX	.외부LOOP의 index update
	COMP	MXLNMO	.배열의 길이보다 WORD가 하나 작은 값과 비교
	JLT	SLOOP	.배열의 길이-3보다 index가 작으면 LOOP 진행
	JEQ	END	.그렇지 않을 경우, sorting 종료
	JGT	END
.SELECTION SORT	 FINISH

.INSERTION SORT
INSERT	LDX	ZERO
	LDA	MAXLEN
	MUL	THREE
	STA	MAXLEN
	SUB	THREE
	STA	MXLNMO
	LDA	THREE
	STA	IDX	.i
	LDA	ZERO
	STA	IDX2	.j
	STA	CORIDX
	STA	TIDX
	STA	TIDX2
	STA	TEMP
	STA	TEMP2
	STA	TEMP3
	STA	TEMP4

ILOOP	LDX	IDX
	LDA	ARR,X
	STA	INST	.insert할 값 저장
	LDA	ZERO
	STA	IDX2	.내부LOOP index 초기화
	SUB	ONE
	STA	CORIDX	.insert할 값의 Index를 -1로 초기화. 음수로 유지될 시, 배열 변화 x
	
IILOOP	LDX	IDX2	
	LDA	ARR,X	. 현재 위치의 배열 값을 불러옴
	COMP	INST	.insert할 값과 비교하여
	JLT	IIIDX
	JEQ	IIIDX	.작거나 같으면 index update로
	STX	CORIDX	.그렇지 않을 경우 CORIDX update할 곳을 정해줌
	LDA	IDX	.i와 j 값을 같게하여 내부 loop를 탈출하도록 함 
	STA	IDX2

IIIDX	COMP	IDX2	.i와 j 값이 같을 경우,
	JEQ	IIF	.내부LOOP를 탈출하여 변경할 값이 있는지 확인하는 IIF로 이동
	LDA	IDX2	.그렇지 않을 경우 index update
	ADD	THREE
	STA	IDX2
	COMP	IDX
	JEQ	IIF
	J	IILOOP

IIF	LDA	CORIDX	
	COMP	ZERO
	JLT	IOIDX	.CORIDX가 -1일 경우, 변경할 것이 없으므로 외부LOOP index update로 이동
	J	IPUSH	.그렇지 않을 경우, insert할 값의 위치를 기준으로 앞의 배열 값들 정렬

IPUSH	LDA	IDX
	STA	TIDX	.k

IFOR	LDX	TIDX
	LDA	TIDX
	JSUB	SISWAP	.위치 k와 k-1의 배열 값을 swap
	LDA	TIDX	
	SUB	THREE	.k값 update
	STA	TIDX
	COMP	CORIDX	.CORIDX 값과 비교하여
	JGT	IFOR	.k 값이 더 클 경우, LOOP 반복 
	JSUB	PRINT	.그렇지 않을 경우, print
	J	IOIDX	.외부 LOOP index update로 jump

IOIDX	LDA	IDX	
	ADD	THREE	.외부LOOP index update
	STA	IDX
	COMP	MAXLEN	.배열의 길이와 비교하여
	JLT	ILOOP	.더 작을 경우 외부LOOP 반복
	JEQ	END	.그렇지 않을 경우, 종료
	JGT	END

.INSERTION SORT FINISH

END	J	END	.프로그램의 끝

SWAP	STL	TEMP3	.PRINT로 JSUB할 떄, L register 값이 변경되기 떄문에 TEMP3에 미리 저장
	LDA	ARR,X
	STA	TEMP2	.현재 위치의 배열 값을 TEMP2에 저장
	LDX	IDX2	
	LDA	ARR,X	.자리를 바꿀 IDX2 위치의 배열 값 불러옴
	LDX	TIDX	.위 LOOP에서 저장한 TIDX의 값을 불러옴
	STA	ARR,X	.TIDX의 위치에 해당하는 배열의 칸에 불러온 IDX2 위치의 배열값을 저장
	LDX	IDX2	
	LDA	TEMP2
	STA	ARR,X	.IDX2 위치의 배열의 칸에 TEMP2에 저장한 값을 저장하고 swap 끝
	LDL	TEMP3	.JSUB으로 인해 변경된 L register의 값을 원래 돌아갈 주소로 변경
	RSUB

SISWAP	LDA	ARR,X
	STA	TEMP3	.현재 위치의 배열 값을 TEMP3에 저장
	LDA	TIDX
	SUB	THREE	.현재 위치의 배열의 앞칸 index를 TIDX2에 저장
	STA	TIDX2
	LDX	TIDX2
	LDA	ARR,X	.현재 위치의 배열의 앞칸 값을 불러옴
	LDX	TIDX	
	STA	ARR,X	.현재 위치에 저장
	LDX	TIDX2
	LDA	TEMP3
	STA	ARR,X	.앞칸에 현재 위치값 저장
	RSUB

PRINT	LDA	ZERO
	STA	PIDX
	STA	PTIDX
	STA	PTEMP
	STA	PTEMP2	.사용할 변수들의 값을 0으로 초기화
	LDX	PIDX

PLOOP	TD	STDOUT
	JEQ	PLOOP
	LDA	ARR,X	.현재 위치에 해당하는 배열 값을 불러옴
FIR	COMP	MAN	
	JGT	FUNC1	.10000보다 크거나 같으면 FUNC1으로 jump
	JEQ	FUNC1
SEC	COMP	CHEON
	JGT	FUNC2	.1000보다 크거나 같으면 FUNC2로 jump
	JEQ	FUNC2
THR	COMP	BAEK
	JGT	FUNC3	.100보다 크거나 같으면 FUNC3로 jump
	JEQ	FUNC3
FTH	COMP	TEN
	JGT	FUNC4	.10보다 크거나 같으면 FUNC4로 jump
	JEQ	FUNC4
	JLT	LAST	.1자리일 경우, 바로 LAST로

LAST	ADD	CONV	.아스키코드로 변환
	WD	STDOUT	.A register에 저장된 값 출력
WRSP	LDA	SPACE	
	WD	STDOUT	.SPACE 공백으로 숫자 구분
	TIX	MAXLEN
	TIX	MAXLEN
	TIX	MAXLEN	.다음 index로 update
	JLT	PLOOP	.베열의 길이보다 작으면 LOOP 진행
	LDA	ENTER	.그렇지 않을 경우, 줄 바꿈
	WD	STDOUT
	RSUB

FUNC1	STA	PTEMP
	DIV	MAN
	ADD	CONV	.10000으로 나누고 아스키코드 값으로 변환하여 10000자리 수 출력
	WD	STDOUT
	SUB	CONV	.다시 정수값으로 변환하고
	MUL	MAN	.10000을 곱해주고
	STA	PTEMP2
	LDA	PTEMP
	SUB	PTEMP2	.원래 값에서 빼주어 나머지 값들을 넘겨줌
	J	FUNC2	.다음 비교로 이동

FUNC2	STA	PTEMP	.위의 FUNC1에서 했던 과정을 1000의 자리에 대해서 실행
	DIV	CHEON
	ADD	CONV
	WD	STDOUT
	SUB	CONV
	MUL	CHEON
	STA	PTEMP2
	LDA	PTEMP
	SUB	PTEMP2
	J	FUNC3

FUNC3	STA	PTEMP	.100의 자리에 대해서 실행
	DIV	BAEK
	ADD	CONV
	WD	STDOUT
	SUB	CONV
	MUL	BAEK
	STA	PTEMP2
	LDA	PTEMP
	SUB	PTEMP2
	J	FUNC4

FUNC4	STA	PTEMP	.10의 자리에 대해서 실행
	DIV	TEN
	ADD	CONV
	WD	STDOUT
	SUB	CONV
	MUL	TEN
	STA	PTEMP2
	LDA	PTEMP
	SUB	PTEMP2
	COMP	ZERO	.10의 경우, 위에서 1을 출력하고 뒤에 0을 출력해줌
	JGT	NEXT4
	WD	STDOUT
NEXT4	J	LAST

STDIN	BYTE	0	.입력
STDOUT	BYTE	1	.출력

.상수값 선언
ONE	WORD	1	
ZERO	WORD	0
THREE	WORD	3
EIGHT	WORD	8
TEN	WORD	10
BAEK	WORD	100
CHEON	WORD	1000
MAN	WORD	10000
MAX	WORD	99999
CTEN	WORD	58
CONV	WORD	48
SPACE	WORD	32
ENTER	WORD	10
EEE	WORD	69
OOO	WORD	79
FFF	WORD	70

.변수 선언
MAXLEN	RESW	1	.배열의 길이
TEMP	RESW	1
TEMP2	RESW	1
TEMP3	RESW	1
TEMP4	RESW	1
TIDX	RESW	1	.임시 저장 공간
IDX	RESW	1
IDX2	RESW	1	.index 관리를 위한 변수
ARR	RESW	8	.8칸의 배열
PTEMP	RESW	1
PTEMP2	RESW	1
PTEMP3	RESW	1
PIDX	RESW	1
PTIDX	RESW	1	.PRINT에서 사용하는 변수들

MXLNMO	RESW	1
TIDX2	RESW	1
MINIDX	RESW	1	.Selection sort에서 최소 값의 index를 저장할 변수
CORIDX	RESW	1	.Insertion soet에서 insert할 값이 들어갈 index를 저장할 변수
INST	RESW	1	.insert할 값을 저장하는 변수
