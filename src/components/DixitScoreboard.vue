<template>
  <div class="scoreboard">
    <h1>Dixit 점수판</h1>
    
    <!-- 플레이어 이름 입력 단계 -->
    <div v-if="!playersSet">
      <h2>플레이어 정보 입력</h2>
      <div v-for="(player, index) in players" :key="index" class="player-input">
        <label>플레이어 {{ index + 1 }}: </label>
        <input v-model="player.name" type="text" placeholder="이름을 입력하세요" />
      </div>
      <button @click="setPlayers" :disabled="!canSetPlayers">설정 완료</button>
    </div>
    
    <!-- 점수판 및 라운드 입력 -->
    <div v-else>
      <!-- 승리 조건 -->
      <div class="settings">
        <h2>설정</h2>
        <label>승리 조건 (최소 점수): </label>
        <input v-model.number="config.winThreshold" type="number" min="1" />
      </div>
      
      <!-- 점수판 테이블 -->
      <h2>점수판</h2>
      <table>
        <thead>
          <tr>
            <th>라운드</th>
            <th v-for="(player, index) in players" :key="index">{{ player.name }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(round, rIndex) in rounds" :key="rIndex">
            <td>{{ rIndex + 1 }}</td>
            <td v-for="(score, pIndex) in round.scores" :key="pIndex">{{ score }}</td>
          </tr>
          <tr class="totals">
            <td>합계</td>
            <td v-for="(player, index) in players" :key="index">{{ player.total }}</td>
          </tr>
        </tbody>
      </table>

      <!-- 라운드 입력 영역 -->
      <div v-if="!gameEnded" class="new-round">
        <h2>새 라운드 점수 입력</h2>
        <div v-for="(player, index) in players" :key="index" class="round-input">
          <label>{{ player.name }}: </label>
          <input v-model.number="newRound[index]" type="number" />
        </div>
        <button @click="addRound">라운드 추가</button>
      </div>
      
      <!-- 게임 종료 알림 -->
      <div v-else class="game-over">
        <h2>게임 종료!</h2>
        <p>승리자: {{ winner }}</p>
        <button @click="onClick">재시작</button>
      </div>
    </div>
  </div>
</template>
  
<script setup>
import { reactive, ref, computed } from 'vue';

/** 4명의 플레이어 초기 정보 (이름은 나중에 입력) */ 
const players = reactive([
    { name: '', total: 0 },
    { name: '', total: 0 },
    { name: '', total: 0 },
    { name: '', total: 0 },
  ]);
  
/** 게임 설정: 승리 조건(최소 점수, 기본값 30점) */
const config = reactive({
    winThreshold: 30,
});
  
/** 각 라운드의 점수 기록 배열 */ 
const rounds = reactive([]);
  
/** 새 라운드 점수 입력 (초기값 0) */ 
const newRound = reactive([0, 0, 0, 0]);
  
/** 게임 종료 상태 */
const gameEnded = ref(false);
/** 승리자 */
const winner = ref('');
  
/** 플레이어 정보 설정 여부 */ 
const playersSet = ref(false);
  
/** 모든 플레이어 이름이 입력되었는지 여부 체크 */ 
const canSetPlayers = computed(() => players.every(player => player.name.trim() !== ''));
  
/** 플레이어 정보 설정 완료 처리 */ 
const setPlayers = () => {
  if (canSetPlayers.value) {
    playersSet.value = true;
  } else {
    alert('모든 플레이어의 이름을 입력해 주세요.');
  }
};
  
/** 새 라운드 추가 및 총점 업데이트 */ 
const addRound = () => {
  // 현재 입력된 라운드 점수를 배열로 복사
  const roundScores = newRound.map(score => Number(score));
    
  // 라운드 기록에 추가
  rounds.push({ scores: roundScores });
    
  // 각 플레이어의 총점 업데이트
  players.forEach((player, index) => {
    player.total += roundScores[index];
  });
    
  /** 라운드 입력 필드 초기화 */
  for (let i = 0; i < newRound.length; i++) {
    newRound[i] = 0;
  }
    
  checkWinningCondition();
};
  
/** 승리 조건 체크 및 게임 종료 처리 */ 
const checkWinningCondition = () => {
  if (gameEnded.value) return;
    
  for (const player of players) {
    if (player.total >= config.winThreshold) {
      gameEnded.value = true;
      winner.value = player.name;

      alert(`게임 종료! 승리자: ${player.name}`);

      break;
    }
  }
};

/** 게임 종료 새로고침 */
const onClick = () => {
  window.location.reload();
};
</script>

<style src="@/assets/css/dixit.css" />