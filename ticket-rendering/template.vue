<template>
  <div class="ticket-outer-wrap" data-rating="{{ ticket_rating }}">
      <div class="highlight-plate {{ highlight_info.plate_class }}" v-if="highlight_info">{{ highlight_info.text }}
          <span class="inline-block">
              {{ highlight_info.price_in_currency }}&nbsp;<span
                      class="currency-code {{ highlight_info.currency }}"></span>
          </span>
      </div>
      <div class="ticket js-ticket" :class="[ticket_type_class, highlight_class, opened_class, expired_class]"
           data-index="{{ index }}">
          <div class="ticket-buy-block" v-if="main_proposal">
              <a class="js-buy-button" data-metainfo="{{ main_proposal.metainfo }}" data-ticket-index="{{ index }}"
                 href="{{ main_proposal.deeplink }}" target="_blank">
                  <div class="price-block {{ expired_price_class }}">
                      <span class="ticket-main-price">
                          {{ main_proposal.price }}
                      </span>
                  </div>
                  <button class="button ticket-buy-button orange-button">
                      <div class="semibold book-now">
                          {{ main_button_text }}
                      </div>
                      <div class="main-proposal-name">Buy on &nbsp;{{ main_proposal.name }}</div>
                  </button>

                  <div class="poposal-original-gate-price-tooltip {{ main_proposal_original_gate_price.expired_price_class }} js-main-original-price" v-if="main_proposal_original_gate_price">
                      <span class="tooltip-price-wrap">
                          {{ main_proposal_original_gate_price.price }}
                          <span class="currency-code {{ main_proposal_original_gate_price.currency }}"> {{ main_proposal_original_gate_price.currency_text }} </span>
                      </span>
                  </div>

              </a>
              <a href="{{ airline_without_price.deeplink }}" v-if="airline_without_price" target="_blank"
                 class="clearfix ticket-proposals js-proposal-link airline-proposal"
                 data-metainfo="{{ airline_without_price.metainfo }}" data-original-index="1"
                 data-ticket-index="{{ airline_without_price.ticket_index }}" data-proposal-index="1">
                      <span class="left proposal-name" title="{{ airline_without_price.name }}">
                          {{ airline_without_price.name }}
                          <span class="ticket-proposals-border-bottom"></span>
                      </span>
              </a>
              <a href="{{ proposal.deeplink }}" target="_blank" v-for="proposal in proposals"
                 class="clearfix ticket-proposals js-proposal-link {{ proposal.airline_proposal }}"
                 data-metainfo="{{ proposal.metainfo }}" data-original-index="{{ proposal.original_index }}"
                 data-ticket-index="{{ proposal.ticket_index }}" data-proposal-index="{{ proposal.proposal_index }}">
                  <span class="left proposal-name" title="{{ proposal.name }}">{{ name }}</span>
                      <span class="right price-container--{{ proposal.currency }}">
                          <span class="button-price">{{ proposal.price }}
                              <span class="currency-code {{ proposal.currency }}"></span>
                          </span>
                      </span>
                      <span class="hidden">
                          <!-- Dafuque is that? -->
                          <div class="poposal-original-gate-price-tooltip" v-if="proposal.original_gate_price">
                              <span class="tooltip-price-wrap">{{ proposal.price }}
                                  <span class="currency-code {{ proposal.currency }}"> {{ proposal.currency_text }} </span>
                              </span>
                          </div>
                      </span>
                  <span class="ticket-proposals-border-bottom"></span>
              </a>

              <span data-index="{{ proposals_count.ticket_index }}" v-if="proposals_count"
                    class="js-more-proposals ticket-proposals more-proposals">{{ proposals_count.value }}</span>
          </div>

          <div class="ticket-info-block">

              <div class="ticket-main-info js-ticket-info" data-index="{{ index }}">
                  <div class="ticket-top-block">
                      <!-- Main Airline logo -->
                      <a class="js-logo-button" href="{{ logo_deeplink }}" data-metainfo="{{ logo_metainfo }}"
                         target="_blank" data-ticket-index="{{ index }}" data-proposal-index="{{ logo_proposal_index }}">
                          <img class="main-airline-logo js-ticket-logo"
                               src="http://pics.avs.io/112/50/{{ carrier_code }}@2x.png" width="112"
                               height="50"/>
                      </a>
                      <!--  ¯\_(ツ)_/¯ -->
                      <div class="top-info">
                          <div class="change-airports-label" v-if="change_airports">
                              <div class="icon icon-change"></div>
                              <span class="name g-uppercase semibold">Change airports</span>
                          </div>
                          <div class="best-ticket-label {{ best_by }}" v-if="best_by">
                              <div class="icon icon-label-corner"></div>
                              <span class="name g-uppercase semibold">{{ best_ticket_label }}</span>
                          </div>
                      </div>
                  </div>
                  <div class="ticket-segments">
                      <div class="segment-block" v-for="segment in segments">
                          <div class="flight-depart-info">
                              <div class="flight-place-title">
                                  <span class="semibold">{{ segment.mini.departure_iata }}</span>
                                  &nbsp;
                                  <span>{{ segment.mini.departure_name }}</span>
                              </div>
                              <div class="flight-date-time">
                                  <div class="flight-time">{{ segment.mini.depart_time }}</div>
                                  <div class="flight-date-wrapper">
                                          <p class="meridiem semibold" v-if="segment.mini.depart_meridiem">{{ segment.mini.depart_meridiem }}</p>
                                      <p class="flight-date">{{ segment.mini.depart_date }}</p>
                                  </div>
                              </div>
                          </div>

                          <div class="flight-duration-info">
                              <div class="stops-info {{ segment.mini.stop_text_class }}">{{ segment.mini.stops_info }}</div>
                              <div class="icon icon-departing-plane"></div>
                              <div class="flight-duration">{{ segment.mini.duration }}</div>
                          </div>

                          <div class="flight-arrive-info">
                              <div class="flight-place-title">
                                  <span>{{ segment.mini.arrival_name }}</span>
                                  &nbsp;
                                  <span class="semibold">{{ segment.mini.arrival_iata }}</span>
                              </div>
                              <div class="flight-date-time">
                                  <div class="flight-date-wrapper">
                                          <div class="meridiem semibold" v-if="segment.mini.arrival_meridiem">{{ segment.mini.arrival_meridiem }}</div>
                                      <div class="flight-date">{{ segment.mini.arrival_date }}</div>
                                  </div>
                                  <div class="flight-time">{{ segment.mini.arrival_time }}</div>
                              </div>
                          </div>
                      </div>
                  </div>
                  <div class="open-ticket-button {{ opened_class }} js-open-ticket-button">
                      <div class="open-ticket-arrow"></div>
                  </div>
                  <div class="js-ticket-tag" id="{{ div_id }}" v-if="ticket_google_tag"></div>
              </div>
              <div class="ticket-details {{ opened_class }}">
                  <template v-for="segment in segments">
                      <div class="segment-container">
                          <div class="direction-title">
                              {{ segment.direction_text }}
                          </div>
                          <div class="segment-flights {{ segment.direction_class }} {{ segment.mini.stop_text_class }}">

                              <template v-for="flight in segment.flights">
                                  <div class="flight-stop clearfix" v-if="flight.stop">
                                      <div class="flight-stop-icon {{ flight.stop.icon }}"></div>
                                      <div class="flight-layover-airport semibold left" v-if="flight.stop.change_airports">
                                          <div class="icon icon-change"></div>
                                          {{ 'Airport_change ' + flight.stop.change_airports.arrival_airport }}
                                          <div class="icon icon-direction-arrow"></div>{{ flight.stop.change_airports.depart_airport }}
                                      </div>
                                      <div class="flight-layover-airport semibold left" v-if="flight.stop.same_airport_layover">{{ 'Stop at ' + flight.stop.same_airport_layover.title }}</div>
                                      <div class="flight-duration semibold right">
                                          {{ flight.stop.duration }}
                                      </div>
                                  </div>

                                  <div class="flight-segment">
                                      <div class="airline-info clearfix">
                                          <div class="airline-logo-container left">
                                              <img class="airline-logo-image js-ticket-logo" width="32" height="32"
                                                   src="http://pics.jetradar.com/al_square/32/32/{{ carrier_code }}@2x.png"/>
                                          </div>
                                          <div class="airline-details left">
                                              <span class="semibold">{{ carrier_name }}</span>
                                              <span class="middot">&middot;</span>
                                              <span> {{ carrier_number }} </span>
                                              <div class="legroom" v-if="legroom">
                                                  Legroom&nbsp;{{ legroom }}&nbsp;cm
                                              </div>
                                          </div>
                                          <div class="airline-features right">
                                              <div class="icon icon-wifi" v-if="wifi">
                                                  <svg width="26px" height="26px" viewBox="0 0 26 26" version="1.1" xmlns="http://www.w3.org/2000/svg">
                                                      <defs></defs>
                                                      <g id="Page-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                                                          <g id="Oval-5-+-Group" transform="translate(1.000000, 1.000000)">
                                                              <circle id="Oval-5" stroke="#DEE4EA" fill="#FFFFFF" cx="12" cy="12" r="12"></circle>
                                                              <g id="Group" transform="translate(6.000000, 7.000000)" fill="#464A4D">
                                                                  <path d="M11.1135,3.72 C10.9171875,3.72 10.720875,3.6451875 10.5710625,3.4951875 C9.3500625,2.2741875 7.726875,1.601625 6,1.601625 C4.2733125,1.601625 2.64975,2.274 1.42875,3.495 C1.129125,3.7948125 0.6435,3.7948125 0.343875,3.495 C0.04425,3.1955625 0.04425,2.7099375 0.343875,2.4103125 C1.85475,0.899625 3.8634375,0.0676875 6,0.0676875 C8.1365625,0.0676875 10.14525,0.8998125 11.6559375,2.4105 C11.9555625,2.710125 11.9555625,3.19575 11.6559375,3.4951875 C11.5063125,3.645 11.3098125,3.72 11.1135,3.72 L11.1135,3.72 Z" id="Shape"></path>
                                                                  <path d="M3.6136875,6.0699375 C3.417375,6.0699375 3.2210625,5.995125 3.07125,5.8455 C2.771625,5.5456875 2.771625,5.06025 3.07125,4.760625 C3.8990625,3.932625 4.9996875,3.476625 6.1704375,3.476625 C7.3411875,3.476625 8.441625,3.932625 9.269625,4.760625 C9.56925,5.06025 9.56925,5.545875 9.2694375,5.8455 C8.9698125,6.14475 8.4841875,6.1449375 8.18475,5.8453125 C7.6468125,5.3071875 6.931125,5.0109375 6.1704375,5.0109375 C5.409375,5.0109375 4.693875,5.3071875 4.1559375,5.8453125 C4.0063125,5.995125 3.81,6.0699375 3.6136875,6.0699375 L3.6136875,6.0699375 Z" id="Shape"></path>
                                                                  <path d="M6.184125,10.0914375 C5.34825,10.0914375 4.668,9.4111875 4.668,8.575125 C4.668,7.7390625 5.34825,7.058625 6.184125,7.058625 C7.020375,7.058625 7.700625,7.7390625 7.700625,8.575125 C7.700625,9.4111875 7.020375,10.0914375 6.184125,10.0914375 L6.184125,10.0914375 Z" id="Shape"></path>
                                                              </g>
                                                          </g>
                                                      </g>
                                                  </svg>
                                              </div>
                                              <div class="tooltip default top tooltip-airline-feature wifi">
                                                  <div class="tooltip-title">
                                                      Wi-Fi
                                                  </div>
                                              </div>
                                              <div class="icon icon-laptop-power" v-if="laptopPower">
                                                  <svg width="26px" height="26px" viewBox="0 0 26 26" version="1.1" xmlns="http://www.w3.org/2000/svg">
                                                      <defs></defs>
                                                      <g id="Page-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                                                          <g id="Group" transform="translate(1.000000, 1.000000)">
                                                              <circle id="Oval-5" stroke="#DEE4EA" fill="#FFFFFF" cx="12" cy="12" r="12"></circle>
                                                              <path d="M8,10 L16,10 L16,12 C16,13.1045695 15.1132936,14 14.0018986,14 L9.99810135,14 C8.89458045,14 8,13.1122704 8,12 L8,10 Z M11,15 L13,15 L13,18 L11,18 L11,15 Z M10,7.49538898 C10,7.2217932 10.2319336,7 10.5,7 C10.7761424,7 11,7.2157526 11,7.49538898 L11,10 L10,10 L10,7.49538898 Z M13,7.49538898 C13,7.2217932 13.2319336,7 13.5,7 C13.7761424,7 14,7.2157526 14,7.49538898 L14,10 L13,10 L13,7.49538898 Z" id="Rectangle-188" fill="#464A4D"></path>
                                                          </g>
                                                      </g>
                                                  </svg>
                                              </div>
                                              <div class="tooltip default top tooltip-airline-feature">
                                                  <div class="tooltip-title">
                                                      Laptop power
                                                  </div>
                                              </div>
                                          </div>
                                      </div>
                                      <div class="segment-info">

                                          <div class="segment-depart">
                                              <div class="segment-info-title semibold">
                                                  {{ depart_iata }}
                                              </div>
                                              <div class="segment-info-name g-text-overflow">
                                                  {{ depart_airport }}
                                              </div>
                                              <div class="segment-info-date">
                                                  <span class="time semibold">
                                                      {{ depart_time }}
                                                  </span>
                                                  &nbsp;
                                                  <span class="date">
                                                      {{ depart_date }}
                                                  </span>
                                              </div>
                                              <div class="icon icon-plane"></div>
                                          </div>
                                          <div class="segment-arrive">
                                              <div class="segment-info-title semibold">
                                                  {{ arrival_iata }}
                                              </div>
                                              <div class="segment-info-name g-text-overflow">
                                                  {{ arrival_airport }}
                                              </div>
                                              <div class="segment-info-date">
                                                  <span class="time semibold">
                                                      {{ arrival_time }}
                                                  </span>
                                                  &nbsp;
                                                  <span class="date">
                                                      {{ arrival_date }}
                                                  </span>
                                              </div>
                                          </div>
                                          <div class="segment-duration">
                                              <div class="segment-info-title">&nbsp;</div>
                                              <div class="segment-info-name g-text-overflow">
                                                  Duration
                                              </div>
                                              <div class="segment-info-date semibold">{{ duration }}</div>
                                          </div>
                                      </div>

                                  </div>
                              </template>
                          </div>
                      </div>
                  </template>
              </div>
          </div>
          <div class="ticket-sharing-block {{ opened_class }}">
              <div class="copy-link-text js-copy-link-text">Share link</div>
              <input type="text" class="copy-link-input js-copy-link-input" readonly="readonly" value="{{ url }}"/>
          </div>
      </div>
  </div>
</template>

<script>
export default {
  data () {
    return {}
  }
}
</script>
