<script>
  // '억'을 계산하기 위한 변수
  const eok = 100000000;
  // 요구수익율 or 회사채 BBB- 5년물 (퍼센트)
  let expect_beta = null;
  // 지배주주 자기자본 (억원)
  let dominant_equity = null;
  //예상 ROE (퍼센트)
  let roe_expected = null;
  //보통주 발행 수
  let normal_stock = null;
  //우선주 발행 수
  let premium_stock = null;
  //자사주 보유 수
  let own_stock = null;
  // 기업가치 (초과수익 반영)
  let corp_value = null;
  // 주식 수
  let stock_count = null;
  //현재 적정가치
  let stock_value = null;
  //연간 10%씩 초과수익율이 낮아진다는 가정
  let future_one = null;
  //연간 20%씩 초과수익율이 낮아진다는 가정
  let future_two = null;

  function calcValue() {
    corp_value =
      dominant_equity +
      (dominant_equity * (roe_expected - expect_beta) * 0.01) /
        (expect_beta * 0.01);
    stock_count = normal_stock + premium_stock - own_stock;
    stock_value = (corp_value * eok) / stock_count;
    future_one =
      ((dominant_equity +
        (dominant_equity * (roe_expected - expect_beta) * 0.01 * 0.9) /
          (1.0 + expect_beta * 0.01 - 0.9)) *
        eok) /
      stock_count;
    future_two =
      ((dominant_equity +
        (dominant_equity * (roe_expected - expect_beta) * 0.01 * 0.8) /
          (1.0 + expect_beta * 0.01 - 0.8)) *
        eok) /
      stock_count;
  }

  function resetData() {
    expect_beta = null;
    dominant_equity = null;
    roe_expected = null;
    normal_stock = null;
    premium_stock = null;
    own_stock = null;
    corp_value = null;
    stock_count = null;
    stock_value = null;
    future_one = null;
    future_two = null;
  }
</script>

<div class="container">
  <main>
    <nav>
      <ul>
        <li><strong>S-RIM 계산기</strong></li>
      </ul>
      <ul>
        <li>
          <a href="https://massivevoid.com" class="contrast">Massive Void</a>
        </li>
      </ul>
    </nav>

    <article>
      <header>주주가치</header>
      <label for="expectedbeta"><small>요구수익율 - %</small></label>
      <input
        id="expectedbeta"
        type="number"
        bind:value={expect_beta}
        placeholder="요구 수익율 (%)"
        required
      />
      <label for="dominantequity"><small>지배주주 자기자본 - 억원</small></label
      >
      <input
        id="dominantequity"
        type="number"
        bind:value={dominant_equity}
        placeholder="지배주주 자기자본 (억 원)"
        required
      />
      <label for="expectedroe"><small>ROE 예상치 - %</small></label>
      <input
        id="expectedroe"
        type="number"
        bind:value={roe_expected}
        placeholder="기대 ROE (%)"
        required
      />
      {#if dominant_equity && roe_expected && expect_beta}
        <footer>
          <p>
            주주가치: {new Intl.NumberFormat().format(
              Math.round(
                dominant_equity +
                  (dominant_equity * (roe_expected - expect_beta) * 0.01) /
                    (expect_beta * 0.01)
              )
            )} 억원
          </p>
        </footer>
      {/if}
    </article>

    <article>
      <header>주식 수</header>
      <label for="normalstock"><small>보통주</small></label>
      <input
        id="normalstock"
        type="number"
        bind:value={normal_stock}
        placeholder="보통주 발행 수량"
      />
      <label for="premiumstock"><small>우선주</small></label>
      <input
        id="premiumstock"
        type="number"
        bind:value={premium_stock}
        placeholder="우선주 발행 수량"
      />
      <label for="ownstock"><small>자사주</small></label>
      <input
        id="ownstock"
        type="number"
        bind:value={own_stock}
        placeholder="자사주 보유 수량"
      />
      {#if normal_stock}
        <footer>
          <p>
            주식 수: {new Intl.NumberFormat().format(
              normal_stock + premium_stock - own_stock
            )} 주
          </p>
        </footer>
      {/if}
    </article>
    {#if stock_value}
      <article>
        <header>주당 가치 (적정가격)</header>
        <table role="grid">
          <thead>
            <tr>
              <th scope="col">#</th>
              <th scope="col">가정</th>
              <th scope="col">주당 가치</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="row">1</th>
              <td>초과수익 수준 유지</td>
              <td
                >{new Intl.NumberFormat().format(Math.round(stock_value))}원</td
              >
            </tr>
            <tr>
              <th scope="row">2</th>
              <td>연간 10% 씩 감소</td>
              <td>{new Intl.NumberFormat().format(Math.round(future_one))}원</td
              >
            </tr>
            <tr>
              <th scope="row">3</th>
              <td>연간 20% 씩 감소</td>
              <td>{new Intl.NumberFormat().format(Math.round(future_two))}원</td
              >
            </tr>
          </tbody>
        </table>
      </article>
    {/if}
    <div class="grid">
      <button on:click={resetData} class="secondary">초기화</button>
      <button on:click={calcValue}>계산하기</button>
    </div>

    <footer>
      © 2023 <a href="https://massivevoid.com" class="secondary">Seongki Sohn</a
      >. All Rights Reserved.
    </footer>
  </main>
</div>
