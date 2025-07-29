<script setup>
import {ref} from "vue"
import { ethers } from 'ethers'

const address = ref()
const balance = ref()
const connectMetamask = async() => {
  if(window.ethereum){
    window.ethereum.request({method: 'eth_requestAccounts'})
    .then(result => {
      accountChangedHandler(result[0])
    })
  }
}

const accountChangedHandler = async(account) => {
  console.log(ethers)
  const accountToUse = Array.isArray(account) ? account[0] : account
  const formattedAccount = ethers.getAddress(accountToUse)
  address.value = formattedAccount
  getUserBalance(formattedAccount)
  await getChainId()
}

const getChainId = async () => {
  try {
    const chainId = await window.ethereum.request({
      method: 'eth_chainId'
    })
    console.log('Chain ID:', chainId)
    return parseInt(chainId, 16)
  } catch (error) {
    console.error('Error getting chain ID:', error)
    return null
  }
}

const getUserBalance = (address) => {
  const formattedAddress = ethers.getAddress(address)
  window.ethereum.request({
    method: 'eth_getBalance',
    params: [formattedAddress, 'latest']
  })
  .then(bal => {
    balance.value = ethers.formatEther(bal)
  })
  .catch(error => {
    console.error('Error getting balance:', error)
    balance.value = 'Error'
  })
}

window.ethereum.on('accountsChanged', accountChangedHandler)
</script>

<template>
  <h1>Connect to the wallet</h1>
  <button @click="connectMetamask">Metamask</button>
  <h3>Address is : {{ address }}</h3>
  <h3>Balance is : {{balance}}</h3>
</template>
