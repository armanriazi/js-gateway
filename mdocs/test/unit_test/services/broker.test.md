import Rabbit from '../../../src/services/broker'

export const brokerTest = () => {
  describe('test broker functions', () => {
    test('returns existing instance of rabbitmq class or creates a new instance and returns it', async () => {
      const rabbitMQ = await Rabbit.getInstance(),
        rabbitMQ2 = await Rabbit.getInstance()

      expect(rabbitMQ).toBe(rabbitMQ2)
    })
  })
}
